---
title: Mijn eerste Python-runbook in Azure Automation | Microsoft Docs
description: Zelfstudie die u bij het maken helpt, testen en publiceren van een eenvoudig runbook voor Python.
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: gwallace
ms.openlocfilehash: 0cf0b1829c7e1f9b0b8be90983a705d82784c062
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2018
---
# <a name="my-first-python-runbook"></a>Mijn eerste Python-runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Deze zelfstudie helpt u bij het maken van een [Python runbook](automation-runbook-types.md#python-runbooks) in Azure Automation. U begint met een eenvoudig runbook dat u testen en publiceren. Vervolgens wijzigt u het runbook zodanig dat Azure-resources daadwerkelijk worden beheerd, in dit geval door een virtuele machine van Azure te starten. Ten slotte u het runbook krachtiger door runbookparameters toe te voegen.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. U stopt en start deze machine, dus het mag geen productiemachine zijn.

## <a name="create-a-new-runbook"></a>Een nieuw runbook maken

U begint met het maken van een eenvoudig runbook waarmee de tekst *Hallo wereld*.

1. Open uw Automation-account in Azure Portal.

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze assets zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. Ook moet u de referentieasset hebben die wordt genoemd in de [vereisten](#prerequisites).<br>

1. Selecteer **Runbooks** onder **Procesbeheer** om de lijst van runbooks te openen.
2. Selecteer **+ een runbook toevoegen** een nieuw runbook maken.
3. Geef het runbook de naam van de *MyFirstRunbook Python*.
4. In dit geval u gaat maken van een [Python runbook](automation-runbook-types.md#python-runbooks) dus selecteer **Python 2** voor **runbooktype**.
5. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="add-code-to-the-runbook"></a>Voeg code toe aan het runbook

Nu toevoegen u een eenvoudige opdracht voor het afdrukken van de tekst "Hallo wereld":

```python
print("Hello World!")
```

Klik op **opslaan** om op te slaan van het runbook.

## <a name="test-the-runbook"></a>Het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, wilt u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, voert u de **concept**versie uit en geeft u de uitvoer interactief weer.

1. Klik op **Testvenster** om het testvenster te openen.
   ![Testvenster](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.
1. Een [runbooktaak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven.
   In eerste instantie is de taakstatus *In de wachtrij geplaatst*. Hiermee wordt aangegeven dat er wordt gewacht tot er in de cloud een runbook worker beschikbaar is. Deze wordt verplaatst naar *starten* wanneer een werknemer de taak claimt en vervolgens *met* wanneer het runbook daadwerkelijk wordt uitgevoerd.
1. Wanneer de runbooktaak is voltooid, wordt de uitvoer ervan weergegeven. U ziet in dit geval *Hallo wereld*.
1. Sluit het testvenster om terug te gaan naar het papier.

## <a name="publish-and-start-the-runbook"></a>Publiceren en het runbook starten

Het runbook dat u hebt gemaakt is nog steeds in de conceptmodus. U moet publiceren voordat u deze in productie kan uitvoeren.
Wanneer u een runbook publiceert, kunt u de bestaande gepubliceerde versie overschrijven met de conceptversie.
In dit geval hebt u nog geen gepubliceerde versie omdat u het runbook zojuist hebt gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.
   ![De knop Publiceren](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. Als u naar links schuift om weer te geven van het runbook in de **Runbooks** deelvenster, het wordt nu een **ontwerpstatus** van **gepubliceerde**.
1. Schuif terug naar rechts om weer te geven van het deelvenster voor **MyFirstRunbook Python**.
   Met de opties bovenaan kunnen we het runbook starten, het runbook weergeven, plannen dat het op een bepaald moment in de toekomst start of een [webhook](automation-webhooks.md) maken, zodat het kan worden gestart via een HTTP-aanroep.
1. U wilt het runbook wordt gestart, dus klik op **Start** en klik vervolgens op **Ok** wanneer de blade Runbook starten wordt geopend.
1. Een taakdeelvenster wordt geopend voor de runbooktaak die u hebt gemaakt. U kunt dit deelvenster sluiten, maar in dit geval u open laten zodat u de voortgang van de taak kan bekijken.
1. De taakstatus wordt weergegeven in **taakoverzicht** en komt overeen met de statussen die u hebt gezien wanneer u het runbook getest.
1. Zodra voor het runbook de status *Voltooid* wordt weergegeven, klikt u op **Uitvoer**. Het deelvenster Uitvoer wordt geopend en ziet u uw *Hallo wereld*.
1. Sluit het deelvenster Uitvoer.
1. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U zou alleen *Hallo wereld* moeten zien in de uitvoerstroom, maar er kunnen ook andere stromen voor een runbooktaak worden weergegeven, zoals Uitgebreid en Fout als hiernaar wordt geschreven met het runbook.
1. Sluit het deelvenster Streams en het deelvenster taak om terug te keren naar het deelvenster MyFirstRunbook-Python.
1. Klik op **Taken** om het deelvenster Taken voor dit runbook te openen. Hiermee worden alle taken weergegeven die met dit runbook zijn gemaakt. U zou slechts één weergegeven taak moeten zien, aangezien de taak slechts eenmaal is uitgevoerd.
1. U kunt op deze taak klikken om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook startte. Hiermee kunt u teruggaan in de tijd en de details bekijken van elke taak die voor een bepaald runbook is gemaakt.

## <a name="add-authentication-to-manage-azure-resources"></a>Verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd.
Voor het beheren van Azure-resources van het script zijn om te verifiëren met de referenties van uw [Automation-account](automation-offering-get-started.md).

> [!NOTE]
> Het Automation-account moet zijn gemaakt met de functie voor de service-principal er een Run as-certificaat.
> Als uw automation-account niet met de service-principal gemaakt is, kunt u verifiëren met behulp van de methode voor [verifiëren met de Azure Management-bibliotheken voor Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Open de teksteditor door te klikken op **bewerken** in het deelvenster MyFirstRunbook-Python.
1. Voeg de volgende code voor verificatie op Azure:
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Code toevoegen om te berekenen van Python-client maken en start de virtuele machine

Om te werken met Azure Virtual machines, geen exemplaar maken van de [Azure Compute-client voor Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

De Compute-client gebruiken om de virtuele machine start. Voeg de volgende code toe aan het runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Waar _MyResourceGroup_ is de naam van de resourcegroep met de virtuele machine en _TestVM_ is de naam van de virtuele machine die u wilt starten. 

Testen en uitvoeren van het runbook opnieuw uit om te zien of deze de virtuele machine wordt gestart.

## <a name="use-input-parameters"></a>Invoerparameters gebruiken

Het runbook wordt momenteel vastgelegde waarden gebruikt voor de namen van de resourcegroep en de virtuele machine.
Nu gaan we code toevoegen die deze waarden opgehaald uit de invoerparameters.

U gebruikt de `sys.argv` variabele om op te halen van de parameterwaarden.
De volgende code toevoegen aan het runbook onmiddellijk na de andere `import` instructies:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

U importeert de `sys` -module, en maakt u twee variabelen op voor de resourcegroep en de VM-namen.
U ziet dat het element van de lijst met argumenten `sys.argv[0]`, is de naam van het script en is geen invoer door de gebruiker.

Nu kunt u de laatste twee regels van het runbook de waarden van de invoerparameter gebruikt in plaats van de vastgelegde waarden wijzigen:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Wanneer u een Python-runbook start (op het **Test** blade of als een gepubliceerd runbook), kunt u de waarden voor parameters in de **Runbook starten** blade onder **Parameters**.

![Tekstvak voor waarde van parameter](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

Nadat u een waarde invoeren in het eerste vak gestart, wordt een tweede weergegeven, enzovoort, zodat u zoveel parameterwaarden kan opgeven indien nodig.

De waarden worden beschikbaar voor het script op als de `sys.argv` matrix zoals in de code die u zojuist hebt toegevoegd.

Voer de naam van de resourcegroep als de waarde voor de eerste parameter en de naam van de virtuele machine te starten als de waarde van de tweede parameter.

![Voer parameterwaarden in de](media/automation-first-runbook-textual-python/runbook-python-params.png)

Klik op **OK** om het runbook te starten. Het runbook wordt uitgevoerd en start de virtuele machine die u hebt opgegeven.

## <a name="next-steps"></a>Volgende stappen

* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks
* Zie [Mijn eerste grafische runbook](automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
* Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks
* Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen
* Zie voor meer informatie over het ontwikkelen voor Azure met Python, [Azure voor Python-ontwikkelaars](https://docs.microsoft.com/python/azure/?view=azure-python).
* Python-2-voorbeeldrunbooks, Zie de [Azure Automation GitHub](https://docs.microsoft.com/python/azure/?view=azure-python).
