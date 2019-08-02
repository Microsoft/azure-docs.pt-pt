---
title: Perguntas frequentes sobre integração de log do Azure | Microsoft Docs
description: Este artigo responde a perguntas sobre a integração de log do Azure.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 7ed63364a9dc4b96470a23af3a4bff832d42621b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727612"
---
# <a name="azure-log-integration-faq"></a>Perguntas frequentes sobre integração de log do Azure

Este artigo responde às perguntas frequentes sobre a integração de logs do Azure.

>[!IMPORTANT]
> O recurso integração de logs do Azure será preterido por 06/15/2019. Os downloads do AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer ao mover para frente examine o [Azure monitor de envio para integração com ferramentas Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

A integração de log do Azure é um serviço do sistema operacional Windows que você pode usar para integrar logs brutos de seus recursos do Azure em seus sistemas de SIEM (gerenciamento de eventos e informações de segurança) locais. Essa integração fornece um painel unificado para todos os seus ativos, locais ou na nuvem. Em seguida, você pode agregar, correlacionar, analisar e alertar para eventos de segurança associados a seus aplicativos.

O método preferencial para integrar os logs do Azure é usando o conector de Azure Monitor do seu fornecedor SIEM e seguindo estas [instruções](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). No entanto, se o fornecedor do SIEM não fornecer um conector para Azure Monitor, você poderá usar a integração de log do Azure como uma solução temporária (se o SIEM tiver suporte da integração de log do Azure) até que esse conector esteja disponível.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="is-the-azure-log-integration-software-free"></a>O software de integração de log do Azure é gratuito?

Sim. Não há nenhum encargo para o software de integração de log do Azure.

## <a name="where-is-azure-log-integration-available"></a>Onde a integração de log do Azure está disponível?

Atualmente, ele está disponível no Azure Commercial e no Azure governamental e não está disponível na China ou na Alemanha.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Como posso ver as contas de armazenamento das quais a integração de log do Azure está obtendo os logs de VM do Azure?

Execute a **lista de origem**do comando AzLog.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Como posso saber em qual assinatura os logs de integração do log do Azure são provenientes?

No caso de logs de auditoria que são colocados nos diretórios **AzureResourcemanagerJson** , a ID da assinatura está no nome do arquivo de log. Isso também é válido para logs na pasta **AzureSecurityCenterJson** . Por exemplo:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Azure Active Directory logs de auditoria incluem a ID do locatário como parte do nome.

Os logs de diagnóstico que são lidos de um hub de eventos não incluem a ID da assinatura como parte do nome. Em vez disso, eles incluem o nome amigável especificado como parte da criação da origem do hub de eventos. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Como posso atualizar a configuração de proxy?

Se a configuração de proxy não permitir o acesso de armazenamento do Azure diretamente, abra o **AZLOG. EXE.** Arquivo de configuração em **C:\Program Files\Microsoft Azure log Integration**. Atualize o arquivo para incluir a seção defaultProxy com o endereço de proxy da sua organização. Depois que a atualização for concluída, pare e inicie o serviço usando os comandos **net stop AzLog** e **net start AzLog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Como posso ver as informações de assinatura em eventos do Windows?

Acrescente a ID da assinatura ao nome amigável ao adicionar a origem:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
O XML do evento tem os seguintes metadados, incluindo a ID da assinatura:

![XML do evento](media/azure-log-integration-faq/event-xml.png)

## <a name="error-messages"></a>Mensagens de erro
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Quando executo o comando ```AzLog createazureid```, por que obtenho o seguinte erro?

Erro:

  *Falha ao criar aplicativo AAD-locatário 72f988bf-86f1-41AF-91ab-2d7cd011db37-Reason = ' proibido '-message = ' privilégios insuficientes para concluir a operação. '*

O comando createazureid do **azlog** tenta criar uma entidade de serviço em todos os locatários do Azure ad para as assinaturas às quais o logon do Azure tem acesso. Se o seu logon do Azure for apenas um usuário convidado nesse locatário do Azure AD, o comando falhará com "privilégios insuficientes para concluir a operação". Peça ao administrador do locatário para adicionar sua conta como um usuário no locatário.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Quando executo o comando **azlog autorizar**, por que obtenho o seguinte erro?

Erro:

  *Aviso ao criar atribuição de função-AuthorizationFailed: O cliente janedo\@Microsoft.com ' com a ID de objeto ' fe9e03e4-4dad-4328-910f-fd24a9660bd2 ' não tem autorização para executar a ação ' Microsoft. Authorization/roleAssignments/Write ' sobre o escopo '/subscriptions/ 70d95299-d689-4c97-b971-0d8ff0000000'.*

O comando **azlog Authorize** atribui a função de leitor à entidade de serviço do Azure AD (criada com **azlog**createazureid) para as assinaturas fornecidas. Se o logon do Azure não for um coadministrador ou um proprietário da assinatura, ele falhará com uma mensagem de erro "falha na autorização". O RBAC (controle de acesso baseado em função do Azure) de coadministrador ou proprietário é necessário para concluir esta ação.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Onde posso encontrar a definição das propriedades no log de auditoria?

Veja:

* [Operações de auditoria com Azure Resource Manager](/azure/azure-resource-manager/resource-group-audit)
* [Listar os eventos de gerenciamento em uma assinatura na API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Onde posso encontrar detalhes sobre os alertas da central de segurança do Azure?

Consulte [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](/azure/security-center/security-center-managing-and-responding-alerts).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Como posso modificar o que é coletado com o diagnóstico de VM?

Para obter detalhes sobre como obter, modificar e definir a configuração de Diagnóstico do Azure, consulte [usar o PowerShell para habilitar o diagnóstico do Azure em uma máquina virtual que executa o Windows](/azure/virtual-machines/windows/ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

O exemplo a seguir obtém a configuração de Diagnóstico do Azure:

    Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

O exemplo a seguir modifica a configuração de Diagnóstico do Azure. Nessa configuração, somente a ID de evento 4624 e a ID de evento 4625 são coletadas do log de eventos de segurança. O Microsoft antimalware para eventos do Azure é coletado do log de eventos do sistema. Para obter detalhes sobre o uso de expressões XPath, consulte consumindo [eventos](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

O exemplo a seguir define a configuração de Diagnóstico do Azure:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Depois de fazer as alterações, verifique a conta de armazenamento para garantir que os eventos corretos sejam coletados.

Se você tiver problemas durante a instalação e a configuração, abra uma [solicitação de suporte](/azure/azure-supportability/how-to-create-azure-support-request). Selecione **integração de log** como o serviço para o qual você está solicitando suporte.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Posso usar a integração de log do Azure para integrar os logs do observador de rede ao meu SIEM?

O observador de rede do Azure gera grandes quantidades de informações de registro em log. Esses logs não devem ser enviados para um SIEM. O único destino com suporte para logs do observador de rede é uma conta de armazenamento. A integração de log do Azure não dá suporte à leitura desses logs e à disponibilização deles para um SIEM.


