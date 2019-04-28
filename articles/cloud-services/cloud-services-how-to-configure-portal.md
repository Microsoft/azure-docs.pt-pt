---
title: Como configurar um serviço cloud (portal) | Documentos da Microsoft
description: Saiba como configurar os serviços cloud no Azure. Saiba como atualizar a configuração do serviço de nuvem e configurar o acesso remoto para instâncias de função. Estes exemplos utilizam o portal do Azure.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jeconnoc
ms.openlocfilehash: 4d8d3b93ef2a6347076fada53932b5fc56838d20
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435874"
---
# <a name="how-to-configure-cloud-services"></a>Como configurar os serviços Cloud

Pode configurar as definições mais utilizadas para um serviço cloud no portal do Azure. Ou, se pretender atualizar diretamente os seus ficheiros de configuração, transfira um ficheiro de configuração de serviços para atualizar e, em seguida, carregue o ficheiro atualizado e atualize o serviço cloud com as alterações de configuração. De uma forma ou de outra, as atualizações de configuração são integradas em todas as instâncias de funções.

Também pode gerir as instâncias das suas funções de serviço cloud ou o ambiente de trabalho remoto neles.

Azure apenas pode garantir a disponibilidade do serviço de 99,95 por cento durante as atualizações de configuração se tiver, pelo menos, duas instâncias de função para cada função. Permite que uma máquina virtual para processar pedidos de cliente, enquanto o outro está a ser atualizado. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Alterar um serviço em nuvem

Depois de abrir o [portal do Azure](https://portal.azure.com/), navegue para o serviço de nuvem. A partir daqui, gerenciar muitos aspectos do mesmo.

![Página de definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)

O **definições** ou **todas as definições** links abrirá **definições** onde pode alterar o **propriedades**, alterar o  **Configuração**, gerir o **certificados**, configure a **regras de alerta**e gerir o **utilizadores** quem tem acesso a este serviço em nuvem.

![Definições do serviço cloud do Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gerir a versão de SO convidado

Por predefinição, o Azure atualiza periodicamente o SO convidado para a imagem mais recente suportada dentro da família de SO que especificou na sua configuração de serviço (. cscfg), como o Windows Server 2016.

Se precisar de uma versão específica do sistema operacional de destino, pode configurá-lo **configuração**.

![Versão de SO do conjunto](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Escolher que uma versão específica do sistema operacional desativa automáticas de SO atualiza e faz a aplicação de patches de sua responsabilidade. Tem de garantir que as instâncias de função estão a receber atualizações ou pode expor seu aplicativo para vulnerabilidades de segurança.

## <a name="monitoring"></a>Monitorização

Pode adicionar alertas ao seu serviço cloud. Clique em **configurações** > **as regras de alerta** > **Adicionar alerta**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

A partir daqui, pode configurar um alerta. Com o **métrica** caixa suspensa, pode configurar um alerta para os seguintes tipos de dados.

* Leitura de disco
* Escrita de disco
* Entrada de rede
* Saída de rede
* Percentagem de CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configurar a monitorização de um mosaico de métrica

Em vez de usar **configurações** > **regras de alerta**, pode clicar em um dos mosaicos de métricos na **monitorização** secção do serviço cloud.

![Monitorização do serviço cloud](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Aqui pode personalizar o gráfico utilizado com o mosaico ou adicionar uma regra de alerta.

## <a name="reboot-reimage-or-remote-desktop"></a>O reinício, a recriação de imagem ou o ambiente de trabalho remoto

Pode configurar o ambiente de trabalho remoto através da [do Azure portal (configurar o ambiente de trabalho remoto)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), ou através de [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Para reiniciar o computador, recriação de imagem ou remoto num serviço em nuvem, selecione a instância do serviço cloud.

![Instância de serviço cloud](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Pode, em seguida, iniciar uma ligação de ambiente de trabalho remota, remotamente, reiniciar a instância ou remotamente a recriar imagem (começar com uma nova imagem) a instância.

![Botões de instância de serviço cloud](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfigurar o. cscfg

Poderá ter de reconfigurar o seu serviço cloud através do [a configuração de serviço (. cscfg)](cloud-services-model-and-package.md#cscfg) ficheiro. Primeiro precisa transferir o ficheiro. cscfg, modificá-lo, em seguida, carregá-lo.

1. Clique nas **definições** ícone ou o **todas as definições** link para abrir **definições**.

    ![Página de definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Clique nas **configuração** item.

    ![Painel de configuração](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Clique no botão **Transferir**.

    ![Transferência](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Depois de atualizar o ficheiro de configuração de serviço, carregar e aplicar as atualizações de configuração:

    ![Carregar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Selecione o ficheiro. cscfg e clique em **OK**.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [implementar um serviço cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurar uma [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerir o seu serviço cloud](cloud-services-how-to-manage-portal.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).
