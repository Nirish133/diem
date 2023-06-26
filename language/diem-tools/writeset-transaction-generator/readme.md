### WriteSet to run custom Move script
The tool can also compile Move script written by the Move oncall into a AdminScript payload. `<NAME_OF_SCRIPT>` is a file name under `language/diem-tools/writeset-transaction-generator/templates` and the `<ARGUMENTS>` should be a json string that would be used to fill in the handlebars template:

`cargo run -- --output <PATH_TO_BLOB> build-custom-script  <NAME_OF_SCRIPT> <ARGUMENTS>`

If Move oncall would like to manually create a WriteSet that rotates the key of an account and reconfigure(only for root account), the oncall should first write down the script to be compiled under `language/diem-tools/writeset-transaction-generator/templates`, in this example, with following scripts:

```
script { 
    use DiemFramework::DiemAccount; 
    use DiemFramework::DiemConfig;
    fun main(account: signer) {
          let account = &account; 
          let new_key = x"{{auth_key}}"; 
          let key_rotation_capability = DiemAccount::extract_key_rotation_capability(account); 
          DiemAccount::rotate_authentication_key(&key_rotation_capability, new_key); 
          DiemAccount::restore_key_rotation_capability(key_rotation_capability);
          //only for root account
          DiemConfig::reconfigure(account);
          } 
    }

  ```
The command to generate the writeset would then be:

`cargo run -- -o rotateauth.bin build-custom-script rotate.move '{"auth_key": "0000000000000000000000000000000000000000000000000000000000000000"}' 00000000000000000000000000000000`
