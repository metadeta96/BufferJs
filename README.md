# BufferJs

An easy yet complete to use iterable data buffer in javascript

Ideal for parsing text data or tokens.

Works with any array-like data.

Compatible with both JavaScript and TypeScript

## Installation

Download from [CDN](https://cdn.jsdelivr.net/npm/buffingjs/dist.browser/buffer.min.js) or install from npm

`npm install --save buffingjs`

## Usage

### Basic Example

```typescript
import { BufferJs } from 'buffingjs';
// Or for javascript
// const BufferJs = require('buffingjs');

const hugeText = `At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis praesentium. 
voluptatum deleniti atque corrupti quos dolores et quas molestias excepturi sint occaecati? 
cupiditate non provident, similique sunt in culpa qui officia deserunt mollitia animi, id est 
laborum et dolorum fuga.`;
const textBuffer = new BufferJs(hugeText);

const list = [1, 2, 3, 4, 5, 6];
const listBuffer = new BufferJs(list);
```

### Moving the cursor

```typescript
import { BufferJs } from 'buffingjs';
// Or for javascript
// const BufferJs = require('buffingjs');

const data = ['A', 'B', 'C', 'D', 'E'];
const buffer = new BufferJs(code);

console.log(buffer.current);    // 'A'
console.log(buffer.next);       // 'B'
console.log(buffer.prev);       // undefined

buffer.consume(2);              // Same as calling buffer.toNext() twice

console.log(buffer.current);    // 'C'
console.log(buffer.next);       // 'D'
console.log(buffer.prev);       // 'B'

console.log(buffer.toPrev());   // 'B'
console.log(buffer.current);    // 'B'
console.log(buffer.next);       // 'A'
console.log(buffer.prev);       // 'C'

console.log(buffer.position);   // 1
buffer.seek(2);
console.log(buffer.position);   // 2

console.log(buffer.current);    // 'C'
console.log(buffer.next);       // 'D'
console.log(buffer.prev);       // 'B'
```

### Parsing

```typescript
import { BufferJs } from 'buffingjs';
// Or for javascript
// const BufferJs = require('buffingjs');

const code = `1+2 - 3 * 5 / 823 + 2.501`;
const buffer = new BufferJs(code);

const regexNumbers = /[\d\.]/;
function parseNumbers() {
    let readDot = false;
    const value = buffer.extract(c => {
        if (c === '.') {
            if (readDot) {
                return false;
            }
            readDot = true;
        }
        return regexNumbers.test(c);
    }).join('');

    return {
        type: 'number',
        value: parseFloat(value)
    };
}

const regexOperators = /^(?:\+|\-|\*|\/|>|<|(?:>>)|(?:<<))$/;
function parseOperators() {
    let value = '';
    buffer.iterate(c => {
        const _possibleMatch = value + c;
        if (!regexOperators.test(_possibleMatch)) {
            return false;
        }
        value = _possibleMatch;
        return true;
    });

    return {
        type: 'operator',
        value
    };
}

const tokens = [];
buffer.forEach(current => {
    if (regexNumbers.test(current)) {
        tokens.push(parseNumbers());
    } else if (regexOperators.test(current)) {
        tokens.push(parseOperators());
    }
});

console.log(tokens);
```

## Buffer Api

**`export`** 

**`class`** Buffer

**`template`** T

### Constructors

####  constructor

\+ **new Buffer**(`data`: ArrayLike‹T›): *[Buffer](README.md)*

Creates an instance of Buffer.

**Parameters:**

Name | Type |
------ | ------ |
`data` | ArrayLike‹T› |

**Returns:** *[Buffer](README.md)*

### Accessors

####  current

• **get current**(): *T | undefined*

The value at the current position

**`readonly`** 

**`type`** (T | undefined)

**Returns:** *T | undefined*

___

####  hasCurrent

• **get hasCurrent**(): *boolean*

Verify if there is a value to read at the current position

**`readonly`** 

**Returns:** *boolean*

___

####  hasNext

• **get hasNext**(): *boolean*

Verify if there is a value to read at the next position

**`readonly`** 

**`type`** boolean

**Returns:** *boolean*

___

####  hasPrev

• **get hasPrev**(): *boolean*

Verify if there is a value to read at the previous position

**`readonly`** 

**`type`** boolean

**Returns:** *boolean*

___

####  length

• **get length**(): *number*

The length of the buffer

**`readonly`** 

**`type`** number

**Returns:** *number*

___

####  next

• **get next**(): *T | undefined*

The next value

**`readonly`** 

**`type`** (T | undefined)

**Returns:** *T | undefined*

___

####  position

• **get position**(): *number*

The current position number

**`readonly`** 

**`type`** number

**Returns:** *number*

___

####  prev

• **get prev**(): *T | undefined*

The previous read value

**`readonly`** 

**`type`** (T | undefined)

**Returns:** *T | undefined*

### Methods

####  at

▸ **at**(`pos`: number, `mode?`: "absolute" | "relative"): *T | undefined*

Returns the value at position [pos]
If mode is 'relative' it is considered from the current position instead of 0
Negative values are accepted in the 'relative' mode
In the 'absolute' mode negative values will always yield undefined

**Parameters:**

Name | Type |
------ | ------ |
`pos` | number |
`mode?` | "absolute" \| "relative" |

**Returns:** *T | undefined*

___

####  consume

▸ **consume**(`count?`: undefined | number): *void*

Consume the value at the current position and move [count] times
Negative values are accepted and move backwards instead

**Parameters:**

Name | Type |
------ | ------ |
`count?` | undefined \| number |

**Returns:** *void*

___

####  drop

▸ **drop**(): *void*

Remove the latest saved position from the stack

**Returns:** *void*

___

####  extract

▸ **extract**(`filter`: function): *T[]*

Iterate from the current position untill [filter] return false
or there is no data to process anymore
Return the iterated data

**Parameters:**

▪ **filter**: *function*

▸ (`next`: T, `pos`: number): *boolean*

**Parameters:**

Name | Type |
------ | ------ |
`next` | T |
`pos` | number |

**Returns:** *T[]*

___

####  forEach

▸ **forEach**(`callback`: function): *T[]*

If the buffer does not change it is position inside the callback

this.consume will be called to ensure an infinite loop is not created by mistake

**Parameters:**

▪ **callback**: *function*

▸ (`next`: T, `pos`: number): *void*

**Parameters:**

Name | Type |
------ | ------ |
`next` | T |
`pos` | number |

**Returns:** *this*

___

####  iterate

▸ **iterate**(`filter`: function): *this*

Iterate from the current position untill [filter] return false
or there is no data to process anymore

**Parameters:**

▪ **filter**: *function*

▸ (`next`: T, `pos`: number): *boolean*

**Parameters:**

Name | Type |
------ | ------ |
`next` | T |
`pos` | number |

**Returns:** *this*

___

####  join

▸ **join**(`sep`: string): *string*

Join the data into a string using the separator [sep]

**Parameters:**

Name | Type |
------ | ------ |
`sep` | string |

**Returns:** *string*

___

####  restore

▸ **restore**(): *void*

Restore to the latest saved position
The position is removed from the stack

**Returns:** *void*

___

####  rewind

▸ **rewind**(): *void*

Return to the starting position of the buffer

**Returns:** *void*
___

####  save

▸ **save**(): *void*

Save the current position for later
Saved positions are stored in stack

**Returns:** *void*

___

####  seek

▸ **seek**(`pos?`: undefined | number): *void*

Change the current position number to [pos]
Values bigger than the buffer legth or less than 0 will be forced to stay in the range

**Parameters:**

Name | Type |
------ | ------ |
`pos?` | undefined \| number |

**Returns:** *void*

___

####  toArray

▸ **toArray**(): *T[]*

**Returns:** *T[]*

___

####  toNext

▸ **toNext**(): *T | undefined*

Move to the next position and then return the value

**Returns:** *T | undefined*

___

####  toPrev

▸ **toPrev**(): *T | undefined*

Move to the previous position and then return the value

**Returns:** *T | undefined*

___

####  toString

▸ **toString**(): *string*

Return the string representation of the data

**Returns:** *string*
