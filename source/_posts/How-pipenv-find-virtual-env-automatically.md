---
title: How pipenv find virtual env automatically
date: 2018-10-29 14:07:54
tags:
---


## 什么是pipenv

> Pipenv is a tool that aims to bring the best of all packaging worlds (bundler, composer, npm, cargo, yarn, etc.) to the Python world. 

Pipenv是pip与virtualenv的结合，可以自动创建python虚拟环境，方便开发和管理依赖。

当我们使用virtualenv来创建虚拟环境的时候，我们需要制定一个目录，通常的做法是放在当前项目的env目录下，然后`source env/bin/activate`。

但我们并不希望将env提交到git仓库中，那么其他人想要开发就需要重新生成这个目录，每次开发都需要执行一次`source`。pipenv为我们解决了这样的问题，它可以让你忽略virtualenv的存在，只需要使用pipenv的命令就可以拥有方便的开发环境。

## pipenv自动创建virtualenv的convention是什么？

在mac中可以看到pipenv生成的virtualenv默认位于像这样的目录中

`$HOME/.local/share/virtualenvs/projectname-hashcode/`

那么后面的`hashcode`究竟是如何生成的呢？我阅读了下pipenv源码研究了下。

在`pipenv/project.py`中有一个方法来获得virtualenv的location

```
@property
def virtualenv_location(self):
    # if VIRTUAL_ENV is set, use that.
    if PIPENV_VIRTUALENV:
        return PIPENV_VIRTUALENV

    if not self._virtualenv_location:  # Use cached version, if available.
        assert self.project_directory, "project not created"
        self._virtualenv_location = self.get_location_for_virtualenv()
    return self._virtualenv_location

def get_location_for_virtualenv(self):
    if self.is_venv_in_project():
        return os.path.join(self.project_directory, ".venv")

    name = self.virtualenv_name
    if self.project_directory:
        venv_path = os.path.join(self.project_directory, ".venv")
        if os.path.exists(venv_path) and not os.path.isdir(".venv"):
            with io.open(venv_path, "r") as f:
                name = f.read().strip()
            # Assume file's contents is a path if it contains slashes.
            if looks_like_dir(name):
                return vistir.compat.Path(name).absolute().as_posix()
    return str(get_workon_home().joinpath(name))
```

当选择在项目内生成venv时，目录名是`.venv`，默认会使用计算的`self.virtualenv_name`

```
@property
def virtualenv_name(self):
    sanitized, encoded_hash = self._get_virtualenv_hash(self.name)
    suffix = "-{0}".format(PIPENV_PYTHON) if PIPENV_PYTHON else ""
    # If the pipfile was located at '/home/user/MY_PROJECT/Pipfile',
    # the name of its virtualenv will be 'my-project-wyUfYPqE'
    return sanitized + "-" + encoded_hash + suffix

 def _get_virtualenv_hash(self, name):
    """Get the name of the virtualenv adjusted for windows if needed

    Returns (name, encoded_hash)
    """

    def get_name(name, location):
        name = self._sanitize(name)
        hash = hashlib.sha256(location.encode()).digest()[:6]
        encoded_hash = base64.urlsafe_b64encode(hash).decode()
        return name, encoded_hash[:8]

    clean_name, encoded_hash = get_name(name, self.pipfile_location)
    venv_name = "{0}-{1}".format(clean_name, encoded_hash)

    # This should work most of the time for
    #   Case-sensitive filesystems,
    #   In-project venv
    #   "Proper" path casing (on non-case-sensitive filesystems).
    if (
        fnmatch.fnmatch("A", "a")
        or self.is_venv_in_project()
        or get_workon_home().joinpath(venv_name).exists()
    ):
        return clean_name, encoded_hash

    # Check for different capitalization of the same project.
    for path in get_workon_home().iterdir():
        if not is_virtual_environment(path):
            continue
        try:
            env_name, hash_ = path.name.rsplit("-", 1)
        except ValueError:
            continue
        if len(hash_) != 8 or env_name.lower() != name.lower():
            continue
        return get_name(env_name, self.pipfile_location.replace(name, env_name))

    # Use the default if no matching env exists.
    return clean_name, encoded_hash
```

这就是pipenv计算hash的方式，可以看到用来生成hash的是`self.pipfile_location`，让我们来确认下这个的值会是什么？

```
@property
def pipfile_location(self):
    if PIPENV_PIPFILE:
        return PIPENV_PIPFILE

    if self._pipfile_location is None:
        try:
            loc = pipfile.Pipfile.find(max_depth=PIPENV_MAX_DEPTH)
        except RuntimeError:
            loc = None
        self._pipfile_location = _normalized(loc)
    return self._pipfile_location
```

在`pipenv/patched/pipfile/api.py`中找到了`find`方法

```
class Pipfile(object):
    @staticmethod
    def find(max_depth=3):
        """Returns the path of a Pipfile in parent directories."""
        i = 0
        for c, d, f in walk_up(os.getcwd()):
            i += 1

            if i < max_depth:
                if 'Pipfile':
                    p = os.path.join(c, 'Pipfile')
                    if os.path.isfile(p):
                        return p
        raise RuntimeError('No Pipfile found!')
```

location将会是Pipfile的absolute path，也就是项目的根目录的绝对路径，pipenv使用这个绝对路径来生成一个hash避免同名项目目录的冲突。
