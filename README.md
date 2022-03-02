# KMS Module

This module allows users to create (or retrieve from data) a key protect instance, create any number of keys, and manage key rings and policies for those keys

## Instance

Users can create their own Key Protect instance, or retrieve one using data with the `kms` variable:
```terraform
variable "kms" {
  description = "Object describing the Key Protect instance"
  type = object({
    name              = string
    use_data          = optional(bool)
    resource_group_id = optional(string)
  })
}
```

## Keys

Any number of keys can be added to a KMS instance using the `kms_keys` variable. The type of the variable is as follows: 
```terraform
  type = list(
    object({
      name            = string
      root_key        = optional(bool)
      payload         = optional(string)
      key_ring        = optional(string) # Any key_ring added will be created
      force_delete    = optional(bool)
      endpoint        = optional(string) # can be public or private
      policies = optional(
        object({
          rotation = optional(
            object({
              interval_month = number
            })
          )
          dual_auth_delete = optional(
            object({
              enabled = bool
            })
          )
        })
      )
      ##############################################################################
      # OPTIONAL - iv_value - Forces New Resource
      # > Used with import tokens. The initialization vector (IV) that is generated 
      # > when you encrypt a nonce. The IV value is required to decrypt the encrypted 
      # > nonce value that you provide when you make a key import request to the 
      # > service. To generate an IV, encrypt the nonce by running 
      # > `ibmcloud kp import-token encrypt-nonce`. Only for imported root key.
      ##############################################################################

      iv_value = optional(string) 

      ##############################################################################
      # OPTIONAL - encrypted_nonce
      # > The encrypted nonce value that verifies your request to import a key to Key 
      # > Protect. This value must be encrypted by using the key that you want to 
      # > import to the service. To retrieve a nonce, use the ibmcloud kp import-token 
      # > get command. Then, encrypt the value by running 
      # > `ibmcloud kp import-token encrypt-nonce.` Only for imported root key.
      ##############################################################################
      
      encrypted_nonce = optional(string) 
    })
  )
  ```

## Module Variables

Name     | Description                                                                                                             | Type
-------- | ----------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
region   | Region where VPC will be created. To find your VPC region, use `ibmcloud is regions` command to find available regions. | string
kms      | Object describing the Key Protect instance                                                                              | object({ name = string use_data = optional(bool) resource_group_id = optional(string) })
kms_keys | List of keys to be created for the service                                                                              | list( object({ name = string root_key = optional(bool) payload = optional(string) key_ring = optional(string) force_delete = optional(bool) endpoint = optional(string) iv_value = optional(string) encrypted_nonce = optional(string) policies = optional( object({ rotation = optional( object({ interval_month = number }) ) dual_auth_delete = optional( object({ enabled = bool }) ) }) ) }) )

## Module Outputs

Name      | Description
--------- | ---------------------------------------
kms_crn   | CRN for KMS instance
kms_guid  | GUID for KMS instance
key_rings | Key rings created by module
keys      | List of names and ids for keys created.