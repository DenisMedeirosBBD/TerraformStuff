# Step 1 - Create volumes.

```
docker volume create --name vault-file
docker volume create --name vault-logs
```

# Step 2 - Run Terraform vault using the volumes above.

```
docker run --restart=always --detach --name vault -p8200:8200 --mount source=vault-file,target=/vault/file --mount source=vault-logs,target=/vault/logs  vault server -dev
```

# Step 3 - Get the root token from the logs.

```
docker logs vault | grep "Root Token"
```
# Step 4 - Acess the web interface and log in using the root token.

```
https://localhost:8200/
```

# Step 5 - Add your vault adress and root token to your environment variables.

```
export VAULT_ADDR='http://127.0.0.1:8200'
export VAULT_DEV_ROOT_TOKEN_ID="yourrootkey"
```
# Step 6 - Configure your terraform to collect data from vault. For example:

```
provider "vault" {
  # This will default to using $VAULT_ADDR
  address = "https://127.0.0.1:8200"
}

resource "vault_generic_secret" "example" {
  path = "secret/foo"

  data_json = <<EOT
{
  "foo":   "bar",
  "pizza": "cheese"
}
EOT
}
```
