---
title: Como configurar um serviço de nuvem (Portal) | Microsoft Docs
description: Saiba como configurar os serviços de nuvem no Azure. Saiba como atualizar a configuração do serviço de nuvem e configurar o acesso remoto a instâncias de função. Esses exemplos usam o portal do Azure.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: gwallace
ms.openlocfilehash: 8b60a81e06b95c69a02f88ff3275743b056c191d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359648"
---
# <a name="how-to-configure-cloud-services"></a>Como configurar os serviços de nuvem

Você pode configurar as configurações mais comumente usadas para um serviço de nuvem no portal do Azure. Ou, se pretender atualizar diretamente os seus ficheiros de configuração, transfira um ficheiro de configuração de serviços para atualizar e, em seguida, carregue o ficheiro atualizado e atualize o serviço cloud com as alterações de configuração. De uma forma ou de outra, as atualizações de configuração são integradas em todas as instâncias de funções.

Você também pode gerenciar as instâncias de suas funções de serviço de nuvem ou a área de trabalho remota nelas.

O Azure só poderá garantir 99,95% de disponibilidade de serviço durante as atualizações de configuração se você tiver pelo menos duas instâncias de função para cada função. Isso permite que uma máquina virtual processe solicitações do cliente enquanto a outra está sendo atualizada. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Alterar um serviço de nuvem

Depois de abrir o [portal do Azure](https://portal.azure.com/), navegue até o serviço de nuvem. A partir daqui, você gerencia muitos aspectos dele.

![Página Configurações](./media/cloud-services-how-to-configure-portal/cloud-service.png)

As **configurações** ou **todos os** links de configurações abrirão **as configurações** em que você pode alterar as **Propriedades**, alterar a **configuração**, gerenciar os **certificados**, configurar **regras de alerta**e gerenciar os **usuários** quem tem acesso a esse serviço de nuvem.

![Configurações do serviço de nuvem do Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gerenciar versão do SO convidado

Por padrão, o Azure atualiza periodicamente seu sistema operacional convidado para a imagem mais recente com suporte na família do sistema operacional que você especificou em sua configuração de serviço (. cscfg), como o Windows Server 2016.

Se você precisar ter como destino uma versão específica do sistema operacional, poderá defini-la na **configuração**do.

![Definir versão do so](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> A escolha de uma versão específica do sistema operacional desabilita as atualizações automáticas do sistema operacional e torna o patch de sua responsabilidade. Você deve garantir que suas instâncias de função estejam recebendo atualizações ou pode expor seu aplicativo a vulnerabilidades de segurança.

## <a name="monitoring"></a>Monitorização

Você pode adicionar alertas ao serviço de nuvem. Clique em **configurações** > **regras** > de alerta**adicionar alerta**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

A partir daqui, você pode configurar um alerta. Com a caixa suspensa **métrica** , você pode configurar um alerta para os tipos de dados a seguir.

* Leitura de disco
* Escrita de disco
* Entrada na Rede
* Saída da Rede
* Percentagem de CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configurar o monitoramento de um bloco de métrica

Em vez de usar **as configurações** > **regras de alerta**, você pode clicar em um dos blocos de métrica na seção **monitoramento** do serviço de nuvem.

![Monitoramento do serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Aqui, você pode personalizar o gráfico usado com o bloco ou adicionar uma regra de alerta.

## <a name="reboot-reimage-or-remote-desktop"></a>Reinicializar, refazer imagem ou área de trabalho remota

Você pode configurar a área de trabalho remota por meio do [portal do Azure (configurar a área de trabalho remota)](cloud-services-role-enable-remote-desktop-new-portal.md), o [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)ou o [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Para reinicializar, refazer a imagem ou remoto em um serviço de nuvem, selecione a instância do serviço de nuvem.

![Instância do serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Em seguida, você pode iniciar uma conexão de área de trabalho remota, reinicializar a instância remotamente ou refazer a imagem remotamente (iniciar com uma imagem atualizada) a instância.

![Botões de instância do serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfigurar seu. cscfg

Talvez seja necessário reconfigurar o serviço de nuvem por meio do arquivo de [configuração de serviço (cscfg)](cloud-services-model-and-package.md#cscfg) . Primeiro, você precisa baixar o arquivo. cscfg, modificá-lo e, em seguida, carregá-lo.

1. Clique no ícone **configurações** ou no link **todas as configurações** para abrir **as configurações**.

    ![Página Configurações](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Clique no item de **configuração** .

    ![Folha de configuração](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Clique no botão **Transferir**.

    ![Transferência](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Depois de atualizar o arquivo de configuração de serviço, carregue e aplique as atualizações de configuração:

    ![Carregar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Selecione o arquivo. cscfg e clique em **OK**.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerencie seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).
