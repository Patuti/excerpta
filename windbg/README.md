# Print current stack showing the relative address (RVA) for the instruction pointer
`dx @$mod_baseaddr_from_addr = (addr) => (@$curprocess.Modules.Where(x => (uint64_t)addr >= x.BaseAddress && (uint64_t)addr < x.BaseAddress + x.Size).First().BaseAddress);`

I haven't figured a way to create a lambda with no arguments, so the less painful is to pass `@$curstack`.

`dx @$prst = (stack) => (stack.Frames.Select(f => new {Name = f.ToDisplayString(), RVA = (uint64_t)f.Attributes.InstructionOffset - @$mod_baseaddr_from_addr(f.Attributes.InstructionOffset), VA = f.Attributes.InstructionOffset}));`

Once both `mod_baseaddr_from_addr` and `prst` lambdas are defined, you can use it like this:

`dx -r2 @$prst(@$curstack);`

## Example output
    [0x0]
        Name             : ntdll!RtlpTpWaitCallback + 0x9b
        RVA              : 0x77cfb
        VA               : 0x7ffe1d607cfb
    [0x1]
        Name             : ntdll!TppExecuteWaitCallback + 0xa4
        RVA              : 0x3eb3c
        VA               : 0x7ffe1d5ceb3c
    [0x2]
        Name             : ntdll!TppWorkerThread + 0x456
        RVA              : 0x12536
        VA               : 0x7ffe1d5a2536
    [0x3]
        Name             : kernel32!BaseThreadInitThunk + 0x14
        RVA              : 0x17034
        VA               : 0x7ffe1bcd7034
    [0x4]
        Name             : ntdll!RtlUserThreadStart + 0x21
        RVA              : 0x4cec1
        VA               : 0x7ffe1d5dcec1