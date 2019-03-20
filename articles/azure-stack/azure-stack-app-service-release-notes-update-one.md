---
title: Serviço de aplicações no notas de versão 1 de atualização do Azure Stack | Documentos da Microsoft
description: Saiba mais sobre as novidades na atualização de um serviço de aplicações no Azure Stack, os problemas conhecidos e onde pode transferir a atualização.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 99b4134f8f2d6ed2f521a7f488c33e06c0f436bb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58165833"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Serviço de aplicações no notas de versão 1 de atualização do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Estas notas de versão descrevem as melhorias e correções no serviço de aplicações do Azure no Azure Stack Update 1 e os problemas conhecidos. Problemas conhecidos são divididos em problemas diretamente relacionados com a implementação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização 1802 seu sistema integrado do Azure Stack ou implementar o development kit do Azure Stack mais recentes antes de implementar o serviço de aplicações do Azure.
>
>

## <a name="build-reference"></a>Criar a referência

O serviço de aplicações no Azure Stack Update 1 número da compilação é **69.0.13698.9**

### <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Novas implementações do serviço de aplicações do Azure no Azure Stack agora exigem uma [certificado de caráter universal de assunto de três](azure-stack-app-service-before-you-get-started.md#get-certificates) devido a melhorias na forma em que o SSO para Kudu agora é processada no serviço de aplicações do Azure. É o novo assunto  **\*. sso.appservice.\< região\>.\< DomainName\>.\< extensão\>**
>
>

Consulte a [documentação antes de começar a utilizar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implementação.

### <a name="new-features-and-fixes"></a>Novas funcionalidades e correções

Serviço de aplicações do Azure no Azure Stack Update 1 inclui as seguintes melhorias e correções:

- **Elevada disponibilidade do serviço de aplicações Azure** -1802 de pilha do Azure a cargas de trabalho de atualização ativada para ser implantado em domínios de falha. Portanto, a infraestrutura do serviço de aplicações é capaz de ser tolerante a falhas, que será implementada em domínios de falha. Por predefinição todas as novas implementações do serviço de aplicações do Azure tem esta capacidade no entanto, para implementações concluídas antes do Azure Stack 1802 atualização que está a ser aplicada consulte o [documentação de domínio de falha de serviço de aplicações](azure-stack-app-service-fault-domain-update.md)

- **Implementar na rede virtual existente** -os clientes podem agora implementar o serviço de aplicações no Azure Stack dentro de uma rede virtual existente. Implantação numa rede virtual existente permite aos clientes ligar ao SQL Server e servidor de ficheiros, necessários para o serviço de aplicações do Azure, através das portas privadas. Durante a implementação, os clientes podem selecionar para implementar numa rede virtual existente, no entanto [tem de criar sub-redes para utilização pelo serviço de aplicações](azure-stack-app-service-before-you-get-started.md#virtual-network) antes da implantação.

- Atualiza para **inquilino de serviço de aplicações, o administrador, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- **Atualizações para as seguintes arquiteturas de aplicações e ferramentas**:
    - Adicionado **.NET Core 2.0** de suporte
    - Adicionado **node. js** versões:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Adicionado **NPM** versões:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Adicionado **PHP** atualizações:
        - 5.6.32
        - 7.0.26 (x86 e x64)
        - 7.1.12 (x86 e x64)
    - Atualizado **Git para Windows** para v 2.14.1
    - Atualizado **Mercurial** para v4.5.0

  - Foi adicionado suporte para **apenas HTTPS** funcionalidade do recurso de domínio personalizado no Portal de inquilino do serviço de aplicações. 

  - Acréscimo da validação da ligação de armazenamento no selecionador de armazenamento personalizado para as funções do Azure 

#### <a name="fixes"></a>Correções

- Ao criar um pacote de implantação offline, os clientes deixará de receber uma mensagem de acesso negado erro ao abrir a pasta do instalador do serviço de aplicações

- Resolver problemas ao trabalhar com a funcionalidade de domínios personalizados no Portal de inquilino do serviço de aplicações.

- Impedir que os clientes que utilizam nomes reservados do administrador durante a configuração

- Ativada a implementação de serviço de aplicações com o **associados a um domínio** servidor de ficheiros

- Obtenção melhorada de raiz do Azure Stack de certificado no script e agora validar o certificado de raiz no instalador do serviço de aplicações.

- Estado incorreto fixo retornado para o Azure Resource Manager quando a subscrição for eliminada que recursos contidos no namespace Microsoft. Web.

### <a name="known-issues-with-the-deployment-process"></a>Problemas conhecidos com o processo de implantação

- Erros de validação de certificado

Alguns clientes tem ocorrido problemas quando o fornecimento de certificados para o instalador do serviço de aplicações durante a implantação num sistema integrado, devido a validação exageradamente restritiva no instalador. O instalador do serviço de aplicações foi relançado, os clientes devem [transfira o instalador atualizado](https://aka.ms/appsvconmasinstaller). Se continuar a ter problemas de validação de certificados com o instalador atualizado, contacte o suporte.

- Problema ao obter o certificado de raiz do Azure Stack do sistema integrado.

Um erro de Get-AzureStackRootCert.ps1 causado aos clientes não conseguir obter o certificado de raiz do Azure Stack, ao executar o script numa máquina que não tenha instalado o certificado de raiz. O script agora também tem sido relançado, resolver este problema e os clientes do pedido [transferir os scripts de programa auxiliar atualizados](https://aka.ms/appsvconmashelpers). Se continuar a ter problemas ao obter o certificado de raiz com o script atualizado, contacte o suporte.

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Não existem não existem problemas conhecidos para a atualização do serviço de aplicações do Azure no Azure Stack Update 1.

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

- Ranhura de troca não funciona

Troca de ranhura do site é apresentada nesta versão. Para restaurar a funcionalidade, conclua estes passos:

1. Modificar o grupo de segurança de rede ControllersNSG para **permitir** conexões remotas de desktop para as instâncias de controlador de serviço de aplicações. Substitua AppService.local com o nome do grupo de recursos que implementou o serviço de aplicações.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. Navegue para o **CN0-VM** em máquinas virtuais no portal do administrador do Azure Stack e **clicar em ligar** para abrir uma sessão de área de trabalho remota com a instância do controlador. Utilize as credenciais especificadas durante a implementação do serviço de aplicações.
3. Inicie **PowerShell como administrador** e execute o seguinte script

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Feche a sessão de ambiente de trabalho remoto.
5. Reverter o grupo de segurança de rede ControllersNSG para **negar** conexões remotas de desktop para as instâncias de controlador de serviço de aplicações. Substitua AppService.local com o nome do grupo de recursos que implementou o serviço de aplicações.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. Os trabalhos são não é possível alcançar o servidor de ficheiros quando o serviço de aplicações é implementado numa rede virtual existente e o servidor de ficheiros só está disponível na rede privada.

Se optar por implementar numa rede virtual existente e um endereço IP interno para se ligar ao seu servidor de ficheiros, tem de adicionar uma regra de segurança de saída, permitindo que o tráfego entre a sub-rede de trabalho e o servidor de ficheiros SMB. Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:

- Origem: Qualquer
- Intervalo de portas de origem: *
- Destino: Endereços IP
- Intervalo de endereços IP de destino: Intervalo de IPs para o servidor de ficheiros
- Intervalo de portas de destino: 445
- Protocolo: TCP
- Ação: Permitir
- Prioridade: 700
- Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para os administradores de nuvem operacional de serviço de aplicações do Azure no Azure Stack

Consulte a documentação no [notas de versão do Azure Stack 1802](azure-stack-update-1802.md)

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral do serviço de aplicações do Azure, consulte [serviço de aplicações do Azure no Descrição geral do Azure Stack](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como preparar a implementação de serviço de aplicações no Azure Stack, veja [antes de começar com o serviço de aplicações no Azure Stack](azure-stack-app-service-before-you-get-started.md).
