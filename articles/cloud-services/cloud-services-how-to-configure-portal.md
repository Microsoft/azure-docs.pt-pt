---
title: Como configurar um serviço de nuvem (portal) [ Microsoft Docs
description: Saiba como configurar serviços de nuvem em Azure. Aprenda a atualizar a configuração do serviço na nuvem e configure o acesso remoto a instâncias de funções. Estes exemplos utilizam o portal Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: e862818a4fe2471af574d153d43f0096af7847b8
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811409"
---
# <a name="how-to-configure-cloud-services"></a>Como configurar um Serviços Cloud

Pode configurar as configurações mais utilizadas para um serviço de nuvem no portal Azure. Ou, se quiser atualizar os seus ficheiros de configuração diretamente, faça o download de um ficheiro de configuração de serviço para atualizar e, em seguida, carregue o ficheiro atualizado e atualize o serviço de nuvem com as alterações de configuração. De qualquer forma, as atualizações de configuração são empurradas para fora para todas as instâncias de função.

Também pode gerir as instâncias das suas funções de serviço na nuvem ou ambiente de trabalho remoto.

O Azure só pode garantir uma disponibilidade de serviço de 99,95% durante as atualizações de configuração se tiver pelo menos duas instâncias de função para cada função. Isso permite que uma máquina virtual processe pedidos de clientes enquanto a outra está a ser atualizada. Para mais informações, consulte acordos de [nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Mude um serviço de nuvem

Depois de abrir o [portal Azure,](https://portal.azure.com/)navegue para o seu serviço de nuvem. A partir daqui, gere-se muitos aspetos.

![Página de Definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)

As **definições** ou **todas as definições** abrirão **Definições** onde pode alterar as **Propriedades,** alterar a **Configuração,** gerir os **Certificados,** configurar **as regras**de Alerta e gerir os **Utilizadores** que tenham acesso a este serviço na nuvem.

![Definições de serviço em nuvem azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gerir a versão osso do hóspede

Por predefinição, o Azure atualiza periodicamente o seu oss o de hóspedes para a mais recente imagem suportada dentro da família OS que especificou na configuração do seu serviço (.cscfg), como o Windows Server 2016.

Se precisar de direcionar uma versão oS específica, pode defini-la na **Configuração**.

![Definir a versão OS](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> A escolha de uma versão oS específica desativa as atualizações automáticas de SO e torna a sua responsabilidade corrigir a sua responsabilidade. Deve certificar-se de que as suas instâncias de função estão a receber atualizações ou pode expor a sua aplicação a vulnerabilidades de segurança.

## <a name="monitoring"></a>Monitorização

Pode adicionar alertas ao seu serviço de nuvem. Clique em **Definições** > **Alert Rules** > Regras de Alerta**Adicionar alerta**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

A partir daqui, pode saqueá-lo. Com a caixa **métrica** de drop-down, pode configurar um alerta para os seguintes tipos de dados.

* Leitura do disco
* Escrita em disco
* Rede em
* Rede para fora
* Percentagem de CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configurar a monitorização a partir de um azulejo métrico

Em vez de utilizar**as Regras**de Alerta de **Definições,** > pode clicar num dos azulejos métricos na secção **de Monitorização** do serviço em nuvem.

![Monitorização do serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

A partir daqui pode personalizar o gráfico utilizado com o azulejo, ou adicionar uma regra de alerta.

## <a name="reboot-reimage-or-remote-desktop"></a>Reiniciar, reimagem ou ambiente de trabalho remoto

Pode configurar o ambiente de trabalho remoto através do [portal Azure (configurar](cloud-services-role-enable-remote-desktop-new-portal.md)o ambiente de trabalho remoto) , [PowerShell,](cloud-services-role-enable-remote-desktop-powershell.md)ou através do [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Para reiniciar, reimagem ou remotamente num Serviço cloud, selecione a instância de serviço na nuvem.

![Instância de serviço em nuvem](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Em seguida, pode iniciar uma ligação remota de ambiente de trabalho, reiniciar remotamente a instância ou reimagem remota (começar com uma imagem nova) a instância.

![Botões de instância de serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfigure o seu .cscfg

Poderá ser necessário reconfigurar o seu serviço de nuvem através do ficheiro [config de serviço (cscfg).](cloud-services-model-and-package.md#cscfg) Primeiro, precisa de descarregar o ficheiro .cscfg, modificá-lo e depois carregá-lo.

1. Clique no ícone **Definições** ou no link **Todas as definições** para abrir **Definições**.

    ![Página de Definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Clique no item de **Configuração.**

    ![Lâmina de configuração](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Clique no botão **Transferir**.

    ![Transferência](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Depois de atualizar o ficheiro de configuração do serviço, faça upload e aplique as atualizações de configuração:

    ![Carregar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Selecione o ficheiro .cscfg e clique em **OK**.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a implementar um serviço de [nuvem.](cloud-services-how-to-create-deploy-portal.md)
* Configure um nome de [domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerencie o seu serviço de cloud](cloud-services-how-to-manage-portal.md).
* Configure [os certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).



