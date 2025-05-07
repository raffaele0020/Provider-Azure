# Provider-Azure
Provider per Azure: AzureRM, AzureAD, AzureDEvOps, AzAPI, Azure Stack;  Azure DevOps: creare una Pipeline con Terraform;

In Terraform, il provider è un plugin che permette di interagire con un servizio cloud per la creazione e gestione dell'infrastruttura.
Quando si crea un'infrastruttura, il primo blocco di configurazione contiene il comando provider, seguito dal nome del provider (ad esempio, azurerm), dalle features e dal subscription_id, che rappresenta l'ID della sottoscrizione del cloud:


```
provider "azurerm" {
  features {} # features è obbligatorio, ma può essere lasciato vuoto
  subscription_id = "ID-della-sottoscrizione"
}
In alcune situazioni, è necessario specificare la versione del provider da utilizzare. Questo accade quando l'infrastruttura è configurata per una versione particolare del provider, o quando si sta modificando un'infrastruttura già esistente, che è stata condivisa da un cliente che ha fornito i file Terraform.
```

Per specificare la versione di un provider, è possibile utilizzare il blocco terraform come segue:

```
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "=3.0.1"
    }
  }
}
```
