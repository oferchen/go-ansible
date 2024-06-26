# Release notes

## v2.0.0 (2024-04-20)

Version 2.0.0 of *go-ansible* introduces several disruptive changes. Read the upgrade guide carefully before proceeding with the upgrade.

### BREAKING CHANGES

> **Note**
> The latest major version of _go-ansible_, version _2.x_, introduced significant and breaking changes. If you are currently using a version prior to _2.x_, please refer to the [upgrade guide](https://github.com/apenella/go-ansible/blob/master/docs/upgrade_guide_to_2.x.md) for detailed information on how to migrate to version _2.x_.

- The Go module name has been changed from `github.com/apenella/go-ansible` to `github.com/apenella/go-ansible/v2`. So, you need to update your import paths to use the new module name.
- The relationship between the executor and `AnsiblePlaybookCmd` / `AnsibleAdhocCmd` / `AnsibleInvetoryCmd` has undergone important changes.
  - **Inversion of responsibilities**: The executor is now responsible for executing external commands, while `AnsiblePlaybookCmd`, `AnsibleInventoryCmd` and `AnsibleAdhocCmd` have cut down their responsibilities, primarily focusing on generating the command to be executed.
  - **Method and Attribute Removal**: The following methods and attributes have been removed on `AnsiblePlaybookCmd`, `AnsibleInventoryCmd` and `AnsibleAdhocCmd`:
    - The `Run` method.
    - The `Exec` and `StdoutCallback` attributes.
  - **Attributes Renaming**: The `Options` attribute has been renamed to `PlaybookOptions` in `AnsiblePlaybookCmd`, `AdhocOptions` in `AnsibleAdhocCmd` and `InventoryOptions` in `AnsibleInventoryCmd`.
- The `Executor` interface has undergone a significant signature change. This change entails the removal of the following arguments `resultsFunc` and `options`. The current signature is: `Execute(ctx context.Context) error`.
- The `github.com/apenella/go-ansible/pkg/options` package has been removed. After that deletion, the attributes from `AnsibleConnectionOptions` and `AnsiblePrivilegeEscalationOptions` attributes have been moved to the `PlaybookOptions`, `AdhocOptions` and `InventoryOptions` structs.
- The `github.com/apenella/go-ansible/pkg/stdoutcallback` package has been removed. Its responsibilities have been absorbed by two distinc packages `github.com/apenella/go-ansible/v2/pkg/execute/result`, which manages the output of the commands, and `github.com/apenella/go-ansible/v2/pkg/execute/stdoutcallback` that enables the setting of the stdout callback.
- The constants `AnsibleForceColorEnv` and `AnsibleHostKeyCheckingEnv` have been removed from the `github.com/apenella/go-ansible/pkg/options` package.
- The functions `AnsibleForceColor`, `AnsibleAvoidHostKeyChecking` and `AnsibleSetEnv` have been removed from the `github.com/apenella/go-ansible/pkg/options` package. Use the `ExecutorWithAnsibleConfigurationSettings` decorator instead defined in the `github.com/apenella/go-ansible/v2/pkg/execute/configuration` package.
- The methods `WithWrite` and `WithShowduration` have been removed from the `ExecutorTimeMeasurement` decorator. Instead, a new method named `Duration` has been introduced for obtaining the duration of the execution.
- In the `AnsiblePlaybookJSONResultsPlayTaskHostsItem` struct, the attributes `StdoutLines` and `StderrLines` have chnage their type from `[]string` to `[]interface{}`.

### Fixed

- Quote properly the attributes `SCPExtraArgs`, `SFTPExtraArgs`, `SSHCommonArgs`, `SSHExtraArgs` in `AnsibleAdhocOptions` and `AnsiblePlaybookOptions` structs when generating the command to be executed. #140
- When using the JSON Stdout Callback method combined with enabled verbosity in the command, it causes an error during JSON parsing. To resolve this issue, the `DefaultExecute` struct includes the `Quiet` method, which removes verbosity from the executed command. #110

### Added

- `AnsibleAdhocExecute` _executor_ has been introduced. That _executor_ allows you to create an executor to run `ansible` commands using the default settings of `DefaultExecute`. This _executor_ is located in the `github.com/apenella/go-ansible/v2/pkg/execute/adhoc` package.
- `AnsibleInventoryExecute` _executor_ has been introduced. That _executor_ allows you to create an executor to run `ansible-inventory` commands using the default settings of `DefaultExecute`. This _executor_ is located in the `github.com/apenella/go-ansible/v2/pkg/execute/inventory` package.
- `ansibleplaybook-embed-python` example to demonstrate how to use `go-ansible` library along with the `go-embed-python` package.
- `ansibleplaybook-extravars` example to show how to configure extra vars when running an Ansible playbook command.
- `ansibleplaybook-ssh` example to show how to execute an Ansible playbook using SSH as the connection method.
- `AnsiblePlaybookExecute` _executor_ has been introduced. That _executor_ allows you to create an executor to run `ansible-playbook` commands using the default settings of `DefaultExecute`. This _executor_ is located in the `github.com/apenella/go-ansible/v2/pkg/execute/playbook` package.
- `Commander` interface has been introduced in the `github.com/apenella/go-ansible/v2/pkg/execute` package. This interface defines the criteria for a struct to be compliant in generating execution commands.
- `ErrorEnricher` interface has been introduced in the `github.com/apenella/go-ansible/v2/pkg/execute` package. This interface defines the criteria for a struct to be compliant in enriching the error message of the command execution.
- `Executabler` interface has been introduced in the `github.com/apenella/go-ansible/v2/pkg/execute` package. This interface defines the criteria for a struct to be compliant in executing external commands.
- `ExecutorEnvVarSetter` interface in `github.com/apenella/go-ansible/v2/pkg/execute/configuration` defines the criteria for a struct to be compliant in setting Ansible configuration.
- `ExecutorQuietStdoutCallbackSetter` interface has been introduced in the `github.com/apenella/go-ansible/v2/pkg/execute/stdoutcallback` package. This interface defines the criteria for a struct to be compliant in setting an executor that accepts the stdout callback configuration and that enables the `Quiet` method for Ansible executions.
- `ExecutorStdoutCallbackSetter` interface has been introduced in the `github.com/apenella/go-ansible/v2/pkg/execute/stdoutcallback` package. This interface defines the criteria for a struct to be compliant in setting an executor that accepts the stdout callback configuration for Ansible executions.
- `github.com/apenella/go-ansible/v2/internal/executable/os/exec` package has been introduced. This package serves as a wrapper for `os.exec`.
- `github.com/apenella/go-ansible/v2/pkg/execute/configuration`  package includes the `ExecutorWithAnsibleConfigurationSettings` struct, which acts as a decorator that facilitates the configuration of Ansible settings within the executor.
- `github.com/apenella/go-ansible/v2/pkg/execute/result/default` package has been introduced. This package offers the default component for printing execution results. It supersedes the `DefaultStdoutCallbackResults` function that was previously defined in the `github.com/apenella/go-ansible/v2/pkg/stdoutcallback` package.
- `github.com/apenella/go-ansible/v2/pkg/execute/result/json` package has been introduced. This package offers the component for printing execution results from the JSON stdout callback. It supersedes the `JSONStdoutCallbackResults` function that was previously defined in the `github.com/apenella/go-ansible/v2/pkg/stdoutcallback` package.
- `github.com/apenella/go-ansible/v2/pkg/execute/stdoutcallback`. package has been introduced and offers multiple decorators designed to set the stdout callback for Ansible executions.
- `github.com/apenella/go-ansible/v2/pkg/execute/workflow` package has been introduced and allows you to define a workflow for executing multiple commands in a sequence.
- `github.com/apenella/go-ansible/v2/pkg/galaxy/collection/install` package has been introduced. This package allows you to install Ansible collections from the Ansible Galaxy. Along with this package, the example `workflowexecute-ansibleplaybook-with-galaxy-install-collection` has been added to demonstrate how to install an Ansible collection and execute an Ansible playbook in a sequence.
- `github.com/apenella/go-ansible/v2/pkg/galaxy/role/install` package has been introduced. This package allows you to install Ansible roles from the Ansible Galaxy. Along with this package, the example `workflowexecute-ansibleplaybook-with-galaxy-install-role` has been added to demonstrate how to install an Ansible role and execute an Ansible playbook in a sequence.
- `golangci-lint` has been added to the CI/CD pipeline to ensure the code quality.
- `NewAnsibleAdhocCmd`, `NewAnsibleInventoryCmd` and `NewAnsiblePlaybookCmd` functions have been introduced. These functions are responsible for creating the `AnsibleAdhocCmd`, `AnsibleInventoryCmd` and `AnsiblePlaybookCmd` structs, respectively.
- `Path` attribute has been added to the `AnsiblePlaybookJSONResultsPlayTaskHostsItem` struct.
- `ResultsOutputer` interface has been introduced in the `github.com/apenella/go-ansible/v2/pkg/execute/result` package.  This interface defines the criteria for a struct to be compliant in printing execution results.
- A utility to generate the code for the configuration package has been introduced. This utility is located in the `utils/cmd/configGenerator.go`.
- The `Quiet` method has been added to the `DefaultExecute` struct. This method forces to remove verbosity from the executed command.

### Changed

- `DefaultExecute` used the `String` method from the `Commander` to include the command in the error message when the execution fails, instead of using the the `String` method from the `os/exec.Cmd` struct.
- In the `AnsiblePlaybookJSONResultsPlayTaskHostsItem` struct, the attributes `StdoutLines` and `StderrLines` have chnage their type from `[]string` to `[]interface{}`.
- The `AnsibleAdhocCmd` struct has been updated to implement the `Commander` interface.
- The `AnsibleInventoryCmd` struct has been updated to implement the `Commander` interface.
- The `AnsiblePlaybookCmd` struct has been updated to implement the `Commander` interface.
- The `AnsiblePlaybookOptions` and `AnsibleAdhocOptions` structs have been updated to include the attributes from `AnsibleConnectionOptions` and `AnsiblePrivilegeEscalationOptions`.
- The `DefaultExecute` struct has been updated to have a new attribute named `Exec` of type `Executabler` that is responsible for executing external commands.
- The `DefaultExecute` struct has been updated to have a new attribute named `Output` of type `ResultsOutputer` that is responsible for printing the execution's output.
- The `DefaultExecute` struct has been updated to implement the `Executor` interface.
- The `DefaultExecute` struct has been updated to implement the `ExecutorEnvVarSetter` interface.
- The `DefaultExecute` struct has been updated to implement the `ExecutorStdoutCallbackSetter` interface.
- The `Execute` method in the `DefaultExecute` struct has been updated to return an error on the deferred function when the command execution fails.
- The `Options` attribute in `AnsibleAdhocCmd` struct has been renamed to `AdhocOptions`.
- The `Options` attribute in `AnsibleInventoryCmd` struct has been renamed to `InventoryOptions`.
- The `Options` attribute in `AnsiblePlaybookCmd` struct has been renamed to `PlaybookOptions`.
- The `Read` method in the `ReadPasswordFromEnvVar` struct from the `github.com/apenella/go-ansible/v2/vault/password/envvars` package has been updated to log a warning message when the environment variable is not set.
- The examples has been adapted to use executor as the component to execute Ansible commands.
- The package `github.com/apenella/go-ansible/pkg/stdoutcallback/result/transformer` has been moved to `github.com/apenella/go-ansible/v2/pkg/execute/result/transformer`.
- Upgrade the Go version from `1.19` to `1.22`.

### Removed

- Remove from `DefaultExecute` ansible-playbook error enrichment.
- The `Exec` attribute has been removed from `AnsiblePlaybookCmd` and `AdhocPlaybookCmd`.
- The `github.com/apenella/go-ansible/pkg/options` package has been removed. After the `AnsibleConnectionOptions` and `AnsiblePrivilegeEscalationOptions` structs are not available anymore.
- The `github.com/apenella/go-ansible/pkg/stdoutcallback` package has been removed.
- The `Run` method has been removed from the `AnsiblePlaybookCmd` and `AdhocPlaybookCmd` structs.
- The `ShowDuration` attribute in the `DefaultExecute` struct has been removed.
- The `StdoutCallback` attribute has been removed from `AnsiblePlaybookCmd` and `AdhocPlaybookCmd`.
- The constants `AnsibleForceColorEnv` and `AnsibleHostKeyCheckingEnv` have been removed from the `github.com/apenella/go-ansible/pkg/options` package.
- The functions `AnsibleForceColor`, `AnsibleAvoidHostKeyChecking` and `AnsibleSetEnv` have been removed from the `github.com/apenella/go-ansible/pkg/options` package. Use the `ExecutorWithAnsibleConfigurationSettings` decorator instead defined in the `github.com/apenella/go-ansible/v2/pkg/execute/configuration` package.
- The methods `WithWrite` and `withshowduration` have been removed from the `ExecutorTimeMeasurement` decorator.
