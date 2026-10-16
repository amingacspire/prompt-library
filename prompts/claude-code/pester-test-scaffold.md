# PowerShell Module Test Scaffold (Pester 5)

## Metadata

- **Category:** Claude Code
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Generates a Pester 5 test file for an existing PowerShell function, with mocks for the Az cmdlets it calls, so the tests run without touching Azure. Use it when a function has no tests and you want a starting point with clear coverage of success, failure, and parameter cases.

The generated tests describe intent from the code. Review each assertion against what the function should do, not what it currently does.

## System Prompt

```
You are a PowerShell test author using Pester 5. Write tests for the function provided. Mock every external cmdlet, including all Az cmdlets, so no test calls Azure. Use Mock with -ModuleName where the function is in a module. Do not change the function under test. Mark any assertion you are unsure of with a comment starting with TODO.
```

## User Prompt

```
Write a Pester 5 test file for the function below. Target environment: {{target_environment}}.

Include:
1. A Describe block named after the function.
2. Context blocks for: valid input, invalid or missing parameters, and cmdlet failure.
3. Mocks for each external cmdlet the function calls, with realistic return objects.
4. Assertions that check output, the calls made (Should -Invoke), and error behavior.
5. A BeforeAll block that imports the module or dot-sources the function.

Function:
{{function_source}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{target_environment}}` | Environment the function is meant for, used to decide which edge cases matter | `non-production` |
| `{{function_source}}` | The function, and the module manifest if the function is exported | Pasted source |

## Example Output

```
Describe 'Get-StorageAccountReport' {
    BeforeAll {
        . $PSScriptRoot\Get-StorageAccountReport.ps1
    }

    Context 'When the resource group has no storage accounts' {
        BeforeAll {
            Mock Get-AzStorageAccount { return @() }
        }
        It 'returns an empty result' {
            Get-StorageAccountReport -ResourceGroupName 'rg-test' | Should -BeNullOrEmpty
        }
    }
}
```

## Notes

- Pester 5 scopes BeforeAll and mocks differently from Pester 4. Use Pester 5 syntax throughout and check the Pester version in use.
- A mock that returns objects shaped differently from the real Az output will hide bugs. Check the return shape against the cmdlet's documentation.
- Tests that pass on generated code are only as good as the assertions. Read each one.
- This prompt has not been tested yet.
