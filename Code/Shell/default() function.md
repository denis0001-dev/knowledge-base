#code #shell #function 

## Description
This function is an alias to:

```bash
if [[ -z "$YOURVARIABLE" ]]; then
	YOURVARIABLE=DEFAULT
fi
```

## Code

```bash
default() {  
  local var="$1"  
  local def="$2"  
  if [ -z "${!var}" ]; then  
    eval "$var=\"$def\""  
  fi  
}
```

## Usage

```bash
default YOURVARIABLE false
```