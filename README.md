# Luajob - A wrapper around vim.loop for job management

Luajob is a library that wraps |vim-loop| with a more |jobstart|-like interface,
which simplifies using async jobs in lua.

## Usage

First you'll need to get load the library

```lua
local luajob = require('luajob')
```

Next create a new job

```lua
local ls = luajob:new({cmd = 'ls'})
```

Finally start the process

```
ls:start()
```

## Example

This example calls "git branch" and sets the current branch to g:git_branch

```lua
local luajob = require('luajob')
local git_branch = luajob:new({
  cmd = 'git branch',
  on_stdout = function(err, data)
    if err then
      print('error:', err)
    elseif data then
      lines = vim.fn.split(data, '\n')
      for _, line in ipairs(lines) do
        if line:find('*') then
          vim.api.nvim_set_var('git_branch', (line:gsub('\n', '')))
        end
      end
    end
  end
})
git_branch:start()
```

## Options

Configuration for the luajob is done by passing a table as an argument to an 
instance of LuaJob the options are similar to that of |jobstart|

cmd : String
  The command to be run in the luajob.
  Example:
  ```lua
  {
    cmd = 'sleep 3'
  }
  ```

cwd : String (Optional)
  The working directory for the command defaults .
  Example:
  ```lua
  {
    cwd = '~'
  }
  ```

env : Table (Optional)
  A table of environment variables to be set for the command.

  Example:
  ```lua
  {
    env = { MYENV = 'env' }
  }
  ```

detach : Boolean (Optional)
  If set the luajob will continue running after Neovim has exited.

  Example:
  ```lua
  {
    detach = true
  }

  ```

on_stdout : Function (Optional)
  The function to be executed when the command outputs to standard output. It 
  will be passed two arguments. The first representing a libuv err on reading,
  and the second representing the outputted data.

  Example:
  ```lua
  {
    on_stdout = function(err, data)
      if err then
        print('error', err)
      elseif data then
        print('data', data)
      end
    end
  }
  ```
  
on_stderr : Function (Optional)
  The function to be executed when the command outputs to standard error. It 
  will be passed two arguments. The first representing libuv err on reading, 
  and the second representing the outputted data.

  Example:
  ```lua
  {
    on_stdout = function(err, data)
      if err then
        print('error', err)
      elseif data then
        print('data', data)
      end
    end
  }
  ```

on_exit : Function (Optional)
  The function to be executed when the command outputs to standard error. It 
  will be passed two arguments. The first representing the exit code, and 
  the second representing the signal. 

  Example:
  ```lua
  {
    on_exit = function(code, signal)
      print('job exited', code, signal)
    end
  }
  ```

