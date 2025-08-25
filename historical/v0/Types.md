Technique.Language
------------------

```haskell
data Technique = Technique
    { techniqueVersion :: Int
    , techniqueLicense :: Rope
    , techniqueCopyright :: Maybe Rope
    , techniqueBody :: [Procedure]
    }

data Procedure = Procedure
    { procedureOffset :: Offset
    , procedureName :: Identifier
    , procedureParams :: [Identifier]
    , procedureInput :: [Type]
    , procedureOutput :: [Type]
    , procedureTitle :: Maybe Markdown
    , procedureDescription :: Maybe Markdown
    , procedureBlock :: Block
    }

newtype Identifier = Identifier Rope
newtype Label = Label Rope
newtype Markdown = Markdown Rope
newtype Type = Type Rope

data Attribute
    = Role Identifier
    | Place Identifier
    | Inherit

data Block = Block [Statement]

data Statement
    = Assignment Offset [Identifier] Expression
    | Execute Offset Expression
    | Comment Offset Rope
    | Declaration Offset Procedure
    | Blank Offset
    | Series Offset

data Expression
    = Application Offset Identifier Expression -- needs to be a procedure
    | None Offset
    | Text Offset Rope
    | Amount Offset Quantity
    | Undefined Offset
    | Object Offset Tablet
    | Variable Offset [Identifier]
    | Operation Offset Operator Expression Expression
    | Grouping Offset Expression
    | Restriction Offset Attribute Block

data Tablet = Tablet [Binding]

data Binding = Binding Label Expression

data Operator
    = WaitEither
    | WaitBoth
    | Combine
```

Technique.Quantity
------------------

```haskell
data Quantity
    = Number Int64
    | Quantity Decimal Decimal Magnitude Symbol

type Symbol = Rope

type Magnitude = Int8
```

Technique.Internal
------------------

```haskell
data Value
    = Unitus
    | Literali Rope
    | Quanticle Quantity
    | Tabularum [(Label,Value)]
    | Parametriq [Value]

newtype Executable = Executable [Function]

data Function
    = Unresolved Identifier
    | Subroutine Procedure Step
    | Primitive Procedure (Value -> Program None Value)

data Step
    = Known Offset Value                           -- literals
    | Bench Offset [(Label,Step)]
    | Depends Offset Name                          -- block waiting on a value
    | NoOp
    | Tuple Offset [Step]
    | Asynchronous Offset [Name] Step              -- assignment
    | Invocation Offset Attribute Function Step    -- function application
    | Nested Offset (DList Step)
```
