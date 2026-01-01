# Terraform Common Errors Guide

Just a simple list of the most common Terraform errors I've run into (and seen others hit) as a DevOps engineer. Each one has a quick explanation, usual causes, and how to fix it.

Terraform is great for IaC, but these errors pop up all the time and can waste hours if you don't know what's going on.

## Common Errors

1. **Error: Dependency cycle detected**  
   Terraform complains about a cycle in your resource dependencies – it can't figure out the order to create things.  
   **Why it happens:**  
   - Two resources point at each other (directly or indirectly).  
   - Overusing `depends_on`.  
   **Fix:**  
   - Go through your dependencies and break the loop.  
   - Only use `depends_on` when Terraform can't figure it out automatically.

2. **Error: Could not lock the state**  
   Happens when Terraform can't lock the state file (especially with remote backends like S3).  
   **Why it happens:**  
   - Someone else is running Terraform on the same workspace.  
   - File permission problems.  
   **Fix:**  
   - Wait for the other run to finish.  
   - If you're sure nothing else is running, use `terraform force-unlock` (be careful!).

3. **Error: Invalid function argument**  
   You passed something wrong to a Terraform function.  
   **Why it happens:**  
   - Wrong number of args or wrong type.  
   - Trying to use a string function on a number, etc.  
   **Fix:**  
   - Check the function docs on HashiCorp's site.  
   - Make sure types match.

4. **Error: Missing required argument**  
   Terraform says a resource or module is missing something it needs.  
   **Why it happens:**  
   - Forgot to set a required field.  
   - Variables not passed into a module.  
   **Fix:**  
   - Look at the provider docs for that resource/module.  
   - Add the missing argument or variable.

5. **Error: Unsupported Terraform Core Version**  
   Your config needs a newer (or different) version of Terraform than you have.  
   **Why it happens:**  
   - `required_version` constraint doesn't match.     
   - Using features from a newer release.  
   **Fix:**  
   - Upgrade Terraform, or loosen the version constraint.

6. **Error: No valid credential sources found for AWS** (same for other providers)  
   Terraform can't find credentials to talk to AWS/GCP/Azure.  
   **Why it happens:**  
   - Env vars missing, ~/.aws/credentials empty or wrong.  
   - Not running on an instance with a role.  
   **Fix:**  
   - Set up credentials properly (env vars, config file, or IAM role).  
   - Quick test: run `aws configure`.

7. **Error: Invalid resource type**  
   Terraform doesn't know what resource you're trying to create.  
   **Why it happens:**  
   - Typo in the resource name.  
   - That resource doesn't exist in your provider version.  
   **Fix:**  
   - Double-check spelling against the docs.  
   - Make sure your provider supports it.

8. **Error: Failed to load plugin**  
   Can't download or load a provider plugin.  
   **Why it happens:**  
   - Provider block missing or wrong version.  
   - Network blocked, or registry down.  
   **Fix:**  
   - Run `terraform init` again.  
   - Check your provider config.

9. **Error: Invalid argument**  
   One of the arguments you gave is wrong or not allowed.  
   **Why it happens:**  
   - Wrong type, deprecated arg, value out of range.  
   **Fix:**  
   - Read the docs for that argument.  
   - Check if it's still supported in your version.

10. **Error: Resource already exists**  
    Terraform wants to create something that's already there.  
    **Why it happens:**  
    - Someone created it manually.  
    - State got out of sync.  
    **Fix:**  
    - Import it: `terraform import <resource_address> <id>`.  
    - Or refresh state with `terraform refresh` (careful with that).
11. **Error: Invalid provider configuration**  
    Terraform fails to initialize a provider due to wrong or missing configuration.  
    **Why it happens:**  
    - Provider block is missing required parameters.  
    - Incorrect version constraints or authentication setup.  
    **Fix:**  
    - Check the provider documentation for required arguments.  
    - Update your provider block and run:  
    ```bash
    terraform init -upgrade
