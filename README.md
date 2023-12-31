# GitHub Action for Deploying Azure AD B2C custom policies

This is a fork of the [azure-ad-b2c/deploy-trustframework-policy](https://github.com/azure-ad-b2c/deploy-trustframework-policy) repository to add in additional error handling and other QOL improvements.

Use this GitHub Action to deploy an [Azure AD B2C custom policy](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview) into your Azure Active Directory B2C tenant using the [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta). If the policy does not yet exist, it will be created. If the policy already exists, it will be replaced.

For more information, see [Deploy Azure AD B2C custom policy with GitHub actions](https://docs.microsoft.com/azure/active-directory-b2c/deploy-custom-policies-github-action).

## Getting Started

```bash
git clone https://github.com/Andrews-McMeel-Universal/deploy-trustframework-policy
```

### Inputs

| Variable | Description | Required | `[Default]`  |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------: | ------------------------------------- |
| `folder` | The folder where the custom policies files are stored | x | `N/A` |
| `files` | Comma delimiter list of policy files | x | `N/A` |
| `tenant` | The full Azure AD B2C tenant name (for example, contoso.onmicrosoft.com) or GUID | x | `N/A` |
| `clientId` | The application Client ID for a service principal which will be used to authenticate to the Microsoft Graph | x | `N/A` |
| `clientSecret` | The application Secret for a service principal which will be used to authenticate to the Microsoft Graph | x | `N/A` |
| `renumberSteps` | Renumber the orchestration steps. Possible values: true, or false |  | `false` |
| `addAppInsightsStep` | Add App Insights orchestration steps to the the user journeys. |  | `false` |
| `verbose` | Log level verbose. |  | `false` |

### Sample workflow

```yaml
on: push

env:
  clientId: 00000000-0000-0000-0000-000000000000
  tenant: my-tenant.onmicrosoft.com

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3

    - name: Upload TrustFrameworkBase Policy
      uses: azure-ad-b2c/deploy-trustframework-policy@v5
      with:
        folder: "./Policies"
        files: "TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml"
        tenant: ${{ env.tenant }}
        clientId: ${{ env.clientId }}
        clientSecret: ${{ secrets.clientSecret }}
        renumberSteps: false
```

---

## Building the Project

### Packaging the Project

To update new version you must package this GitHub Action. Use the following commands to package the project:

```bash
npm run-script build  
npm run-script package
```

You can find more information about these scripts in the [package.json](package.json) file. For example:

```json
"scripts": {
    "build": "tsc",
    "format": "prettier --write **/*.ts",
    "format-check": "prettier --check **/*.ts",
    "lint": "eslint src/**/*.ts",
    "package": "ncc build --source-map --license licenses.txt",
    "test": "jest",
    "all": "npm run build && npm run format && npm run lint && npm run package && npm test"
  }
```

After the build is completed, you can see that the JavaScript files under the [dist](dist) folder changed with the latest version of your TypeScript code.

### Build issues

The GitHub build runs the scrips as described above. The `lint` script runs the  [eslint](https://eslint.org/) command. This command analyzes your code to quickly find problems. You can change the settings of the eslint command in the [.eslintrc.json](.eslintrc.json) file. The following example suppresses some of the errors:

```json
"rules": {
    "i18n-text/no-en": 0,
    "import/named": "warn",
    "github/no-then": "warn",
    "eslint-comments/no-use": "off",
    "import/no-namespace": "off",
    "no-unused-vars": "off",
```

### Test the action

When you commit a change to any branch or a PR, the [test.yml](.github/workflows/test.yml) workflow runs with `clientId` parameter set to `test`. The `test` value indicates to the GitHub Action to exit the test successfully. We exit the test because because the  required parameters are not configured in this repo.

To test the GitHub Action create your own repo, add the workflow. Then configure the [uses](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepsuses) to point to your branch, fork, or commit. The following example demonstrate how to configure the workflow to use the latest commit in the `vNext` branch.

```bash
- name: 'Upload custom policies'
  uses: azure-ad-b2c/deploy-trustframework-policy@vNext
```

---

## Reusable Workflow Integration

Once a pull request is merged into _main_, you can create a new release to use it as a reusable workflow. To create a new release, follow the instructions in this guide: [Creating a Release](https://amuniversal.atlassian.net/wiki/spaces/TD/pages/3452043300/Creating+a+new+GitHub+Release#Creating-a-release)

### Update Major Release

Once you've created a new release, you can use the [Update Major Release Workflow](https://github.com/Andrews-McMeel-Universal/deploy-trustframework-policy/actions/workflows/update-major-release.yaml) to automatically update the major release tag for the repository.

1. Navigate to the [Update Major Release](https://github.com/Andrews-McMeel-Universal/deploy-trustframework-policy/actions/workflows/update-major-release.yaml) workflow.
1. Press "Run workflow" on the right-hand side of the page.
1. Specify the tag to create a major release for and what the major release will be.
1. Click "Run workflow"
