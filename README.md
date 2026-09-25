# unhandledexceptionpolicy

A single config file that stops an unhandled exception on a background thread from killing
GTA IV.

## What it does

LCPDFR's `AudioHelper` starts bare threads that call into `SoundEngine`. When a DirectSound
device cannot be created, that throw reaches the top of a thread with no handler, and since
.NET 2.0 the runtime terminates the process for that. Under .NET 1.x such an exception was
swallowed and only that thread died.

`GTAIV.exe.config` restores the old behaviour, turning a crash to desktop into a dead audio
thread:

    <configuration>
      <runtime>
        <legacyUnhandledExceptionPolicy enabled="1" />
      </runtime>
    </configuration>

ScriptHookDotNet hosts CLR v4.0.30319 through `ICLRRuntimeHost`, and the setting is process
wide, read from the executable's config file.

## Installing

Copy `GTAIV.exe.config` next to `GTAIV.exe`. Deleting it fully reverts the change. GTA IV
itself is native and never reads it.

## Scope, and what this is not

This does not fix any underlying defect. It stops one class of defect from taking the whole
game down, which buys time to fix the real thing. For the leak that motivated it, see [lcpdfr-1.1-audiofix](https://github.com/ipkpjersi/lcpdfr-1.1-audiofix).

Verify it is actually in effect with [throwtest](https://github.com/ipkpjersi/throwtest-iv), which throws on a bare
thread on purpose.
