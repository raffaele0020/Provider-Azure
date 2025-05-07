# Provider-Azure
Provider per Azure: AzureRM, AzureAD, AzureDEvOps, AzAPI, Azure Stack;  Azure DevOps: creare una Pipeline con Terraform;


## *Azure RM*
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


![image](https://github.com/user-attachments/assets/f44aebbb-d668-49f7-920b-1198bd8c9412)

Azure Resource Manager (ARM) è il servizio di gestione e distribuzione delle risorse in Azure. Consente agli utenti di creare, gestire, leggere, aggiornare e cancellare risorse come macchine virtuali (VM), account di archiviazione (Storage Accounts), reti virtuali (VNet) e molto altro.

Con questo provider, facciamo riferimento alla gestione delle risorse cloud, mentre con Azure Active Directory (Azure AD) ci occupiamo della gestione delle identità.

Questo servizio è accessibile attraverso diversi strumenti, tra cui il portale di Azure, PowerShell, Azure CLI e le API REST.

Risorse (Resources)
Le risorse in Azure sono tutti gli oggetti configurabili, come ad esempio VM, reti virtuali (VNet), account di archiviazione (Storage Accounts), ecc.

Gruppi di risorse (Resource Groups)
I gruppi di risorse sono contenitori logici dove vengono organizzate e gestite le risorse. Ogni risorsa viene creata all'interno di un gruppo di risorse, che aiuta a organizzare e gestire meglio l'infrastruttura.

Ad esempio, se stiamo creando un'infrastruttura per lo sviluppo, possiamo creare un gruppo di risorse chiamato "Dev" con il seguente codice Terraform:

```
resource "azurerm_resource_group" "Dev" {
  name     = "Dev"
  location = "West Europe"
}
```
Successivamente, quando configuriamo una nuova risorsa, come una rete virtuale (VNet) per lo sviluppo (VNet-dev), la inseriamo nel gruppo di risorse "Dev", come mostrato di seguito:

```
resource "azurerm_virtual_network" "VNet_Dev" {
  name                = "VNet-Dev"
  resource_group_name = azurerm_resource_group.Dev.name
  location            = azurerm_resource_group.Dev.location
  address_space       = ["10.0.0.0/16"]
}
```
In questo esempio, la rete virtuale VNet_Dev viene creata all'interno del gruppo di risorse Dev, utilizzando le stesse informazioni di posizione del gruppo.


## *Azure AD*

![image](https://github.com/user-attachments/assets/093dccc3-83dc-4715-bb1a-5bcebe860b39)

Azure Active Directory (Azure AD) è il servizio di gestione delle identità e degli accessi di Microsoft. Consente di:

Autenticare utenti e dispositivi (SSO, MFA)

Gestire permessi e ruoli (RBAC)

Integrare applicazioni (Microsoft 365, app personalizzate)

Autenticare servizi cloud (Azure, Microsoft 365, app SaaS)

Il provider azuread di Terraform permette di gestire risorse di Azure AD (utenti, gruppi, applicazioni) utilizzando le Microsoft Graph API. È complementare al provider azurerm.

Configurazione di Terraform:
```
terraform {
  required_providers {
    azuread = {
      source  = "hashicorp/azuread"
      version = "~> 2.15.0"
    }
  }
}
```
Configurazione del provider azuread:
```
provider "azuread" {
  tenant_id = "00000000-0000-0000-0000-000000000000"
}
Esempio di creazione risorse:
```
Creare un'applicazione:

```
resource "azuread_application_registration" "example" {
  display_name = "ExampleApp"
}
```
Creare un service principal:

```
resource "azuread_service_principal" "example" {
  client_id = azuread_application_registration.example.client_id
}
```
Creare un utente:

```
resource "azuread_user" "example" {
  user_principal_name = "ExampleUser@${data.azuread_domains.example.domains.0.domain_name}"
  display_name        = "Example User"
  password            = "..."
}
```
Questo ti permette di gestire identità e risorse di Azure AD con Terraform.

## *Azure DevOps*
![image](https://github.com/user-attachments/assets/914498b3-7214-4a78-8296-a7d93ed8000b)

Azure DevOps è una piattaforma di Microsoft per il ciclo di vita completo dello sviluppo software, che include:

Repos: sistema di controllo versione basato su Git

Pipelines: CI/CD per automatizzare integrazione, test e rilascio, supportando YAML pipelines, multi-stage e ambienti multi-cloud

Boards: gestione di task e sprint

Il provider azuredevops di Terraform permette di gestire risorse di Azure DevOps tramite le REST API, come progetti, pipeline, repository, ecc.

Configurazione di Terraform:
```
terraform {
  required_providers {
    azuredevops = {
      source  = "microsoft/azuredevops"
      version = ">= 0.1.0"
    }
  }
}
```
Creare un progetto in Azure DevOps:
```
resource "azuredevops_project" "project" {
  name        = "Project Name"
  description = "Project Description"
}
```
Metodi di autenticazione per Azure DevOps:
Personal Access Token (PAT): per ambienti locali o pipeline, con permessi e scadenza specifici.

OpenID Connect (OIDC): per integrazione con sistemi automatizzati come GitHub Actions o Terraform Cloud.

Impostazioni:

```
use_oidc = true
oidc_token_file_path = "path_to_token_file"
oidc_token_request_url = "url_for_dynamic_token"
```
Azure AD/Entra ID:

Autenticazione tramite app registrata con client_id, client_secret, e tenant_id.

Managed Identity (MSI): per ambienti Azure con identità gestita (ad esempio, VM o App Service).

```
use_msi = true
```
Questi metodi permettono di autenticarsi in modo sicuro con Azure DevOps, sia per l'utilizzo locale che per ambienti automatizzati.

## *Azure API Provider*

![image](https://github.com/user-attachments/assets/7a4cc2d1-aae4-4139-8bb5-5b7d3479ca92)

Il provider AzAPI di Terraform consente di gestire risorse di Azure che non sono ancora supportate dai provider classici come azurerm. Questo è particolarmente utile per:

Risorse in anteprima (preview)

Feature sperimentali

Personalizzazioni particolari

Il provider azurerm ha un ciclo di rilascio più lento e potrebbe non supportare risorse recenti o in anteprima. AzAPI permette di colmare questa lacuna, interfacciandosi direttamente con le API ARM di Azure.

Esempio di configurazione e utilizzo del provider azapi:
1. Definire il provider in Terraform:
```
terraform {
  required_providers {
    azapi = {
      source = "azure/azapi"
    }
  }
}

provider "azapi" {}

```

2. Creare una risorsa con AzAPI:
In questo esempio, creiamo una risorsa di tipo roleAssignments per gestire le assegnazioni di ruoli in Azure, utilizzando la versione preview dell'API.

```
resource "azapi_resource" "example" {
  type      = "Microsoft.Authorization/roleAssignments@2020-04-01-preview"
  name      = "example-role-assignment"
  parent_id = data.azurerm_resource_group.example.id
  body      = jsonencode({
    properties = {
      roleDefinitionId = "/subscriptions/.../roleDefinitions/..."
      principalId      = "..."
    }
  })
}
```
Dettagli:
azapi_resource: risorsa generica per interagire con le API ARM di Azure tramite AzAPI.

type: specifica il tipo di risorsa che stai creando o gestendo, ad esempio Microsoft.Authorization/roleAssignments.

body: permette di definire il corpo della richiesta JSON utilizzando la funzione jsonencode per formattare i dati in modo corretto.

Questo ti consente di gestire risorse non ancora supportate dai provider ufficiali di Terraform come azurerm, sfruttando le API dirette di Azure.

## *Azure Stack*

![image](https://github.com/user-attachments/assets/a9376d68-1684-4041-8de3-59442dc42e64)

Azure Stack è una soluzione di Microsoft progettata per ambienti con limitata connettività al cloud o con requisiti di compliance, non solo per l'uso "on-premises". Esistono diverse versioni di Azure Stack:

Azure Stack Hub: una piattaforma completa che consente di eseguire servizi Azure localmente, inclusi VM, app e servizi PaaS. È destinato a service provider e aziende con esigenze di isolamento.

Azure Stack HCI: una soluzione ibrida per infrastruttura iperconvergente, ottimizzata per Azure Hybrid.

Azure Stack Edge: un'appliance per edge computing, gestione dei carichi di lavoro offline e AI locale, gestita da Microsoft.

Configurazione del provider Azure Stack in Terraform
Il provider azurestack permette di gestire risorse su Azure Stack, ecco un esempio di configurazione:

1. Configurazione del provider Azure Stack:
```
provider "azurestack" {
  # Si consiglia di bloccare la versione del provider
  # version = "=0.9.0"
}
```
2. Creazione di un gruppo di risorse:
```
resource "azurestack_resource_group" "test" {
  name     = "production"
  location = "West US"
}
```
3. Creazione di una rete virtuale nel gruppo di risorse:
```
resource "azurestack_virtual_network" "test" {
  name                = "production-network"
  address_space       = ["10.0.0.0/16"]
  location            = azurestack_resource_group.test.location
  resource_group_name = azurestack_resource_group.test.name

  subnet {
    name           = "subnet1"
    address_prefix = "10.0.1.0/24"
  }

  subnet {
    name           = "subnet2"
    address_prefix = "10.0.2.0/24"
  }

  subnet {
    name           = "subnet3"
    address_prefix = "10.0.3.0/24"
  }
}
```
Dettagli:
Provider Azure Stack: consente di interagire con Azure Stack, gestendo risorse come gruppi di risorse, reti virtuali e subnet.

Rete virtuale (Virtual Network): definisce una rete con più subnet e uno spazio di indirizzi personalizzato.

Sottoreti (Subnets): è possibile definire più sottoreti con indirizzi IP specifici per segmentare la rete.

Questo ti permette di creare e gestire infrastrutture Azure Stack, sia per ambienti on-premises che per soluzioni ibride.


## *Azure DevOps : creare una Pipeline con Terraform*
![image](https://github.com/user-attachments/assets/b9aebdea-ea4d-4ece-88dc-18a22fbab766)


Requisiti
Abbonamento Azure: Devi avere un abbonamento Azure per poter utilizzare Terraform e Azure DevOps.

Configurazione Terraform: Assicurati che i file di configurazione Terraform siano configurati correttamente.

Progetto in Azure DevOps: Devi avere un progetto Azure DevOps. Puoi crearne uno seguendo la guida crea un progetto in Azure DevOps.

Estensione Terraform per Azure DevOps: Installa l'estensione delle attività di build e rilascio per Terraform, disponibile su Marketplace di Visual Studio.

Connessione al servizio di Azure: Devi concedere a Azure DevOps l'accesso alla tua sottoscrizione Azure. Crea una connessione di servizio chiamata terraform-basic-testing-azure-connection in Azure DevOps per connetterti alle sottoscrizioni Azure.

Passaggi di Configurazione
1. Scarica il codice di esempio:
Scarica il progetto di test di integrazione da GitHub. La directory in cui scarichi l'esempio è definita come la directory di esempio. Puoi trovare il progetto di esempio su GitHub: Terraform Example Project.

2. Verifica la configurazione Terraform locale:
Esegui i seguenti comandi nella tua directory di esempio, sotto la cartella src, per convalidare i tuoi file di configurazione Terraform:

Inizializza Terraform:

```
terraform init
```
Verifica la sintassi della configurazione:

```
terraform validate
```
Se la configurazione è valida, vedrai un messaggio che indica che la configurazione è corretta.

Se inserisci un errore (ad esempio, un errore di digitazione nel file main.tf), vedrai un messaggio di errore che ti indicherà il punto in cui la sintassi è errata.

3. Esempio di file YAML per Azure DevOps Pipeline:
Il file YAML definisce la pipeline di Azure DevOps che esegue il processo di Terraform init e Terraform validate. Ecco un esempio di file YAML:
```
yaml
Copia
trigger:
  branches:
    include:
      - main
      - develop

pool:
  vmImage: 'ubuntu-latest'

variables:
  azureServiceConnection: 'terraform-basic-testing-azure-connection'
  terraformWorkingDirectory: 'src'

stages:
  - stage: TerraformValidation
    displayName: "Terraform Init & Validate"
    jobs:
      - job: ValidateTerraform
        displayName: "Init and Validate Terraform Config"
        steps:
          - task: Checkout@1
            displayName: "Checkout repository"

          - task: TerraformInstaller@1
            displayName: "Install Terraform"
            inputs:
              terraformVersion: 'latest'

          - task: TerraformTaskV4@4
            displayName: "Terraform Init"
            inputs:
              provider: 'azurerm'
              command: 'init'
              workingDirectory: '$(terraformWorkingDirectory)'
              backendServiceArm: '$(azureServiceConnection)'

          - task: TerraformTaskV4@4
            displayName: "Terraform Validate"
            inputs:
              provider: 'azurerm'
              command: 'validate'
              workingDirectory: '$(terraformWorkingDirectory)'
              environmentServiceNameAzureRM: '$(azureServiceConnection)'
```
Cosa fa questa pipeline?
Trigger: La pipeline è attivata per i branch main e develop.

Pool: Utilizza un VM Ubuntu per eseguire la pipeline.

Variabili:

azureServiceConnection: Connessione al servizio Azure DevOps.

terraformWorkingDirectory: La cartella src contiene i file di configurazione Terraform.

Stadi:

Terraform Init: Inizializza la directory di lavoro con terraform init.

Terraform Validate: Esegui la convalida della sintassi dei file di configurazione Terraform con terraform validate.

Requisiti per farla funzionare
Estensione Terraform per Azure DevOps: Devi installare l'estensione per Terraform in Azure DevOps, come quella di JasonBJohnson o Microsoft.

Connessione al servizio ARM: Devi creare una connessione al servizio chiamata terraform-basic-testing-azure-connection per consentire ad Azure DevOps di connettersi al tuo abbonamento Azure.

Conclusioni
Questa configurazione ti consente di automatizzare il processo di validazione della configurazione Terraform tramite Azure DevOps. La pipeline esegue automaticamente il processo di terraform init e terraform validate ogni volta che una modifica viene effettuata sui branch main o develop, riducendo il rischio di errori durante il deployment e garantendo che le modifiche non interrompano l'infrastruttura esistente.





