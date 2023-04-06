# NeoViM Reference

## Variables

### Getting
To read the value assigned to a variable, use `set` syntax with `?`, e.g.
```
" Gets value assigned to 'filetype' variable
:set filetype?
```

### Setting
To assign a value to a variable, use `set` syntax with `=`, e.g.
```
:set filetype=vim
```

### Notable variables
|Name                     |Type     |Description                                             |
|-------------------------|---------|--------------------------------------------------------|
|filetype                 | string  | The syntax highlighting applied to current buffer      |
|tabstop                  | number  | Number of spaces that a <Tab> in the file counts for   |
|shiftwidth               | number  | Number of spaces to use for each step of (auto)indent  |
|expandtab                | boolean | Use the appropriate number of spaces to insert a <Tab> |

## Commands

## Configuration
