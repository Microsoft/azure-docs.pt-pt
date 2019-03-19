---
title: Integração de registos do Azure FAQ | Documentos da Microsoft
description: Este artigo responde a perguntas sobre a integração de registos do Azure.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 4f6a724fe6c1e8668084f1c1cefbaa01cffba181
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005848"
---
# <a name="azure-log-integration-faq"></a>Integração de registos do Azure FAQ

Este artigo responde às perguntas mais frequentes (FAQ) sobre a integração de registo do Azure.

>[!IMPORTANT]
> A funcionalidade de integração de registo do Azure vai ser preterida até 06/01/2019. Downloads de AzLog foram desativados a 27 de Junho de 2018. Para obter orientações sobre o que fazer a postagem de revisão de encaminhamento de movimentação [monitor do Azure de utilização para integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integração de registos do Azure é um serviço de sistema operativo do Windows que pode utilizar para integrar registos não processados do seus recursos do Azure em seus sistemas no local segurança informações e eventos (SIEM) de gestão. Esta integração fornece um dashboard unificado para todos os seus ativos, no local ou na cloud. Pode, em seguida, Agregar, correlacionar, analisar e de alertas para eventos de segurança associados a seus aplicativos.

O método preferido para a integração de registos do Azure é utilizando o conector do Azure Monitor do seu fornecedor SIEM e seguir estas [instruções](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). No entanto, se o seu fornecedor SIEM não fornecer um conector para o Azure Monitor, poderá conseguir utilizar a integração de registo do Azure como uma solução temporária (se a sua SIEM é suportado pelo Azure Log Integration) até que tal um conector esteja disponível.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="is-the-azure-log-integration-software-free"></a>O software de integração de registos do Azure é gratuito?

Sim. Não existe nenhum custo associado para o software de integração de registos do Azure.

## <a name="where-is-azure-log-integration-available"></a>Onde está disponível integração de registos do Azure?

Ele está atualmente disponível no Azure comercial e o Azure Government e não está disponível na China ou Alemanha.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Como posso ver as contas de armazenamento a partir do qual o Azure Log Integration está obtendo os registos de VM do Azure?

Execute o comando **lista de origem AzLog**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Como posso saber qual assinatura os registos de integração de registos do Azure são de?

No caso de registos de auditoria que são colocados no **AzureResourcemanagerJson** diretórios, a subscrição do ID é o nome de ficheiro de registo. Isso também é verdadeiro para os registos no **AzureSecurityCenterJson** pasta. Por exemplo:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Registos de auditoria do Azure Active Directory incluem o ID de inquilino como parte do nome.

Os registos de diagnóstico que são lidos a partir de um hub de eventos não incluem o ID de subscrição como parte do nome. Em vez disso, eles incluem o nome amigável, especificado como parte da criação da origem do hub de eventos. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Como posso atualizar a configuração de proxy?

Se a sua definição de proxy não permite o acesso de armazenamento do Azure diretamente, abra o **AZLOG. EXE. CONFIG** de ficheiros **c:\Program Files\Microsoft Azure Log Integration**. Atualizar o ficheiro para incluir o **defaultProxy** secção com o endereço de proxy da sua organização. Após a atualização estar concluída, parar e iniciar o serviço ao utilizar os comandos **net parar AzLog** e **net iniciar AzLog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress="http://127.0.0.1:8888"
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Como posso ver as informações de subscrição de eventos do Windows?

Acrescente o ID de subscrição para o nome amigável ao adicionar a origem:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
O XML do evento tem os seguintes metadados, incluindo o ID de subscrição:

![Evento XML][1]

## <a name="error-messages"></a>Mensagens de erro
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Quando executo o comando ```AzLog createazureid```, por que obtenho o erro seguinte?

Erro:

  *Falha ao criar a aplicação do AAD - inquilino 72f988bf-86f1-41af-91ab-2d7cd011db37-razão = "Proibido" - mensagem = "Privilégios insuficientes para concluir a operação."*

O **azlog createazureid** tenta criar um principal de serviço em todos os inquilinos do Azure AD para as subscrições que o início de sessão do Azure tem acesso ao comando. Se o seu início de sessão do Azure é apenas um utilizador convidado nesse inquilino do Azure AD, o comando falha com "Privilégios insuficientes para concluir a operação." Peça ao administrador de inquilinos para adicionar a sua conta como um utilizador no inquilino.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Quando executo o comando **azlog autorizar**, por que obtenho o erro seguinte?

Erro:

  *Aviso de criação da atribuição de função - AuthorizationFailed: O cliente janedo\@microsoft.com' com o objeto id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no âmbito "/subscriptions/ 70d 95299-d689-4C 97-b971-0d8ff0000000'.*

O **azlog autorizar** comando atribui a função de leitor ao principal de serviço do Azure AD (criado com **azlog createazureid**) para as subscrições fornecidas. Se o início de sessão do Azure não é um coadministrador ou um proprietário da subscrição, ele falhará com uma mensagem de erro "Falha na autorização". Do Azure com base em função de controlo de acesso (RBAC) de coadministrador ou proprietário é necessário para concluir esta ação.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Onde posso encontrar a definição das propriedades no log de auditoria?

Veja:

* [Auditar operações com o Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Listar os eventos de gestão numa subscrição na API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Onde posso encontrar detalhes sobre os alertas do Centro de segurança do Azure?

Ver [gerir e responder a alertas de segurança no Centro de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Como posso modificar o que é recolhido com os diagnósticos VM?

Para obter detalhes sobre como obter, modificar e definir a configuração de diagnósticos do Azure, consulte [utilize o PowerShell para ativar o diagnóstico do Azure numa máquina virtual a executar o Windows](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

O exemplo seguinte obtém a configuração de diagnósticos do Azure:

    Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

O exemplo seguinte modifica a configuração de diagnósticos do Azure. Nesta configuração, apenas o ID do evento 4624 e o evento ID 4625 são recolhidos a partir do registo de eventos de segurança. O Microsoft Antimalware para eventos do Azure são recolhidos a partir do registo de eventos do sistema. Para obter detalhes sobre o uso de expressões XPath, consulte [consumir eventos](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

O exemplo seguinte define a configuração de diagnósticos do Azure:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Depois de efetuar alterações, verifique a conta de armazenamento para garantir que os eventos corretos são recolhidos.

Se tiver quaisquer problemas durante a instalação e configuração, abra um [pedido de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Selecione **integração de registos** como o serviço para o qual está a solicitar suporte.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Posso utilizar o Azure Log Integration para integrar registos de observador de rede no meu SIEM?

O observador de rede do Azure gera grandes quantidades de informações de registo. Estes registos não se destinam a serem enviados para um SIEM. O destino suportado apenas para os registos do observador de rede é uma conta de armazenamento. Integração de registos do Azure não suporta estes registos de leitura e tornando-os disponíveis para um SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
