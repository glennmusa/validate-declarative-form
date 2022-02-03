# validate-declarative-form

![tests](https://github.com/glennmusa/validate-declarative-form/actions/workflows/tests.yml/badge.svg?branch=main)

A Python command-line utility for validating that the outputs of a given Declarative Form Azure Portal UI JSON template map to the input parameters of a given ARM Deployment Template JSON template.

```plaintext
Declarative Form Output => ARM Template Parameters
```

## What is a Declarative Form?

Developers describe custom Azure Portal user interfaces for ARM deployment templates as JSON.

For many years, [Create UI Definition](https://docs.microsoft.com/en-us/azure/azure-resource-manager/managed-applications/create-uidefinition-overview) (also known as `createUiDefinition.json` and `CUID`) was both the JSON schema and named mechanism for creating custom Azure Portal user interfaces.

[Declarative Form](https://github.com/Azure/portaldocs/blob/1fe62c54c1e87aadade061bab70e810efb22713a/portal-sdk/generated/portalfx-cuid.md#introduction-to-declarative-form) is Create UI Definition's successor.

## Running the Validator locally

Run the `--help` command to see the required arguments:

```plaintext
python validate_declarative_form.py -h
```

And you should get back some help:

```plaintext
usage: validate_declarative_form.py [-h] form_template_path deployment_template_path 

Validate a Declarative Form UI template against an ARM Deployment Template.
```

And can execute it like:

```plaintext
python validate_declarative_form.py sample-templates/form.json sample-templates/template.json
```

## Running the Validator in an automated workflow

I could use the validator on a \*nix agent with `python3` by downloading the validator script from this repository, then passing my form template and deployment template as arguments to that script: 

```plaintext
curl -s \
  -o validate_declarative_form.py \
  -L https://raw.githubusercontent.com/glennmusa/validate-declarative-form/main/validate_declarative_form.py

python validate_declarative_form.py path/to/a/form.json path/to/a/template.json
```

### Success

If successful, you'll find this in the stdout:

```plaintext
Success!
```

### Failure

Any failure will print the errors to stderr and exit code 1.

Some example errors:

```plaintext
Required parameter 'fizz' not found in Declarative Form output
```

```plaintext
Form output 'fizz2' not found in deployment template parameters
```

```plaintext
File could not be found: samples/form2.json
```

## Debugging source or running tests

This repository defines a [dev container](https://microsoft.github.io/code-with-engineering-playbook/developer-experience/devcontainers/) that has everything you need to get up and running quickly with all the tools and configuration you need already installed configured.

It's really simple to get started now in your browser on a [GitHub Codespace](https://docs.github.com/en/codespaces/getting-started/quickstart):

- on this repository, click the green `<> Code` button then `New Codespace`

If you prefer local development, you can use this same dev container in Visual Studio Code:

- [Visual Studio Code with Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

## Debugging the Validator

### Debugging in the devcontainer

There's `launch.json` editor settings specified that pass command line arguments to any active Python script:

- Open `validate_declarative_form.py` in the devcontainer
- set a breakpoint
- press `F5` to start debugging

See <https://code.visualstudio.com/docs/python/debugging/> for more information on how to debug Python from the Codespace.

## Testing the Validator

### Testing in the devcontainer

There's `settings.json` editor settings specified that inform where Python unit tests can be discovered:

- Select "Testing" from the Application menu (or open the Command Pallete (F1) and type `View: Show Testing`)
- Expand the `validation` directory and it's children to see all the unit tests
- You can run them all selecting the "Run Tests" button (or open the Command Pallete (F1) and type `Test: Run All Tests`)
- You can debug individual tests by setting a breakpoint in `validate_declarative_form.py` or `validate_declarative_form_test.py` and selecting `Debug Test` from the tests pane

### Testing from the terminal

You can run the unit tests by calling the `unittest` module from Python like:

```plaintext
python -m unittest validate_declarative_form_test.py -v
```

And you should get output similar to:

```plaintext
test_form_specifies_all_required_parameters (validate_declarative_form_test.ValidateDeclarativeFormTest)
Test that all required parameters not output by the form return errors ... ok
test_form_specifies_valid_parameters (validate_declarative_form_test.ValidateDeclarativeFormTest)
Test that all form outputs exist as deployment template parameters ... ok
test_get_required_parameters (validate_declarative_form_test.ValidateDeclarativeFormTest)
Test that all deployment template parameters that do not have default values are returned ... ok
test_validate_form_captures_extraneous_form_output (validate_declarative_form_test.ValidateDeclarativeFormTest)
Test that if there are extra form outputs that are not template parameters ... ok
test_validate_form_captures_missing_required_parameter (validate_declarative_form_test.ValidateDeclarativeFormTest)
Test that if there is a missing required template parameter ... ok
test_validate_form_catches_invalid_template_json (validate_declarative_form_test.ValidateDeclarativeFormTest)
Test that if invalid JSON is passed that load fails and ... ok
test_validate_form_without_errors (validate_declarative_form_test.ValidateDeclarativeFormTest)
Test that if there are no errors in the validation ... ok

----------------------------------------------------------------------
Ran 7 tests in 0.001s

OK
```
