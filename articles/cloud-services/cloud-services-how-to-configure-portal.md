---
title: Como configurar um serviço de nuvem (portal) Microsoft Docs
description: Saiba como configurar serviços em nuvem em Azure. Aprenda a atualizar a configuração do serviço de nuvem e configuure o acesso remoto a instâncias de função. Estes exemplos usam o portal Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: 4f2b5716e253f2810ff953a10c879574c6a6c342
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576664"
---
# <a name="how-to-configure-cloud-services"></a>Como configurar um Serviços Cloud

Pode configurar as definições mais utilizadas para um serviço de nuvem no portal Azure. Ou, se gostar de atualizar diretamente os seus ficheiros de configuração, descarregue um ficheiro de configuração de serviço para atualizar e, em seguida, carrevar o ficheiro atualizado e atualizar o serviço de cloud com as alterações de configuração. De qualquer forma, as atualizações de configuração são empurradas para todas as instâncias de função.

Também pode gerir as instâncias das suas funções de serviço na nuvem ou ambiente de trabalho remoto neles.

O Azure só pode garantir a disponibilidade de serviço de 99,95% durante as atualizações de configuração se tiver pelo menos duas instâncias de função para cada função. Isto permite que uma máquina virtual processe os pedidos dos clientes enquanto a outra está a ser atualizada. Para mais informações, consulte [os Contratos de Nível de Serviço.](https://azure.microsoft.com/support/legal/sla/)

## <a name="change-a-cloud-service"></a>Alterar um serviço de nuvem

Depois de abrir o [portal Azure,](https://portal.azure.com/)navegue para o seu serviço na nuvem. A partir daqui, geres muitos aspetos.

![Página de definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)

As **definições** ou **todas as configurações** abrirão **Configurações** onde pode alterar as **Propriedades**, alterar a **Configuração**, gerir os **Certificados,** configurar **as regras de Alerta**e gerir os **Utilizadores** que tenham acesso a este serviço na nuvem.

![Definições de serviço em nuvem Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gerir a versão OS do Guest

Por padrão, o Azure atualiza periodicamente o seu SO convidado para a mais recente imagem suportada dentro da família OS que especificou na sua configuração de serviço (.cscfg), como o Windows Server 2016.

Se precisar de direcionar uma versão de SO específica, pode defini-la na **Configuração**.

![Definir versão OS](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> A escolha de uma versão so específica desativa as atualizações automáticas de SO e torna a correção da sua responsabilidade. Tem de garantir que as suas funções estão a receber atualizações ou pode expor a sua aplicação a vulnerabilidades de segurança.

## <a name="monitoring"></a>Monitorização

Pode adicionar alertas ao seu serviço na nuvem. Clique **em Definições**  >  **Regras de Alerta**Adicionar  >  **alerta**.

![Screenshot da panela De Definições com a opção de regras de alerta realçada e delineada a vermelho e a opção de alerta Add delineada a vermelho.](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

A partir daqui, pode configurar um alerta. Com a caixa de entrega **métrica,** pode configurar um alerta para os seguintes tipos de dados.

* Leitura de disco
* Escrita em disco
* Rede em
* Rede para fora
* Percentagem de CPU

![Screenshot do painel de regras de alerta Adicionar com todas as opções de configuração definidas.](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configure a monitorização de um azulejo métrico

Em vez **Settings**de utilizar as  >  **Regras de Alerta de**Definições, pode clicar num dos azulejos métricos da secção de **Monitorização** do serviço de nuvem.

![Monitorização do serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

A partir daqui pode personalizar o gráfico usado com o azulejo, ou adicionar uma regra de alerta.

## <a name="reboot-reimage-or-remote-desktop"></a>Reiniciar, reimagem ou ambiente de trabalho remoto

Pode configurar um ambiente de trabalho remoto através do [portal Azure (configurar o ambiente de trabalho remoto)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell,](cloud-services-role-enable-remote-desktop-powershell.md)ou através [do Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Para reiniciar, reimagem ou remoto num Serviço de Cloud, selecione a instância de serviço de cloud.

![Instância de serviço em nuvem](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Em seguida, pode iniciar uma ligação remota no ambiente de trabalho, reiniciar remotamente a instância ou reimagem remotamente (começar com uma nova imagem) o caso.

![Botões de instância de serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfigure o seu .cscfg

Poderá ser necessário reconfigurar o seu serviço de cloud através do ficheiro [config de serviço (cscfg).](cloud-services-model-and-package.md#cscfg) Primeiro, tem de descarregar o seu ficheiro .cscfg, modificá-lo e depois carregá-lo.

1. Clique no ícone **Definições** ou no link **Todas as definições** para abrir **Definições**.

    ![Página de definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Clique no item **De Configuração.**

    ![Lâmina de configuração](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Clique no botão **Transferir**.

    ![Download](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Depois de atualizar o ficheiro de configuração do serviço, faça upload e aplique as atualizações de configuração:

    ![Carregar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Selecione o ficheiro .cscfg e clique **em OK**.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [implementar um serviço de cloud](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado.](cloud-services-custom-domain-name-portal.md)
* [Gerencie o seu serviço na nuvem](cloud-services-how-to-manage-portal.md).
* Configure [os certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).



