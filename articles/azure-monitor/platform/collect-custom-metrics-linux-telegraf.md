---
title: Coletar métricas personalizadas para uma VM Linux com o agente InfluxData Telegraf
description: Coletar métricas personalizadas para uma VM Linux com o agente InfluxData Telegraf
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e8164a111b9ad5ebcc67c248586e2576046334b0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883249"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Coletar métricas personalizadas para uma VM Linux com o agente InfluxData Telegraf

Usando Azure Monitor, você pode coletar métricas personalizadas por meio de sua telemetria do aplicativo, um agente em execução em seus recursos do Azure ou até mesmo sistemas de monitoramento externos. Em seguida, você pode enviá-los diretamente para Azure Monitor. Este artigo fornece instruções sobre como implantar o agente do [InfluxData](https://www.influxdata.com/) telegraf em uma VM do Linux no Azure e configurar o agente para publicar métricas para Azure monitor. 

## <a name="influxdata-telegraf-agent"></a>Agente InfluxData Telegraf 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) é um agente orientado por plug-ins que habilita a coleta de métricas de mais de 150 fontes diferentes. Dependendo de quais cargas de trabalho são executadas em sua VM, você pode configurar o agente para aproveitar os plug-ins de entrada especializados para coletar métricas. Os exemplos são MySQL, NGINX e Apache. Usando plug-ins de saída, o agente pode gravar em destinos que você escolher. O agente Telegraf integrado diretamente com a API REST de métricas personalizadas Azure Monitor. Ele dá suporte a um plug-in de saída Azure Monitor. Usando esse plug-in, o agente pode coletar métricas específicas de carga de trabalho em sua VM Linux e enviá-las como métricas personalizadas para Azure Monitor. 

 ![Visão geral do agente Telegraph](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas 

Para este tutorial, implantamos uma VM do Linux que executa o sistema operacional Ubuntu 16, 4 LTS. O agente Telegraf tem suporte para a maioria dos sistemas operacionais Linux. Os pacotes Debian e RPM estão disponíveis juntamente com os binários do Linux não empacotados no [portal de download do InfluxData](https://portal.influxdata.com/downloads). Consulte este [Guia de instalação do Telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) para obter instruções e opções adicionais de instalação. 

Inicie sessão no [portal do Azure](https://portal.azure.com).

Criar uma nova VM do Linux: 

1. Selecione a opção **criar um recurso** no painel de navegação à esquerda. 
1. Pesquisar **máquina virtual**.  
1. Selecione **Ubuntu 16, 4 LTS** e selecione **criar**. 
1. Forneça um nome de VM como **MyTelegrafVM**.  
1. Deixe o tipo de disco como **SSD**. Em seguida, forneça um **nome de usuário**, como **azureuser**. 
1. Para **tipo de autenticação**, selecione **senha**. Em seguida, insira uma senha que você usará posteriormente para SSH nessa VM. 
1. Escolha **criar novo grupo de recursos**. Em seguida, forneça um nome,como MyResource. Escolha seu **local**. Em seguida, selecione **OK**. 

    ![Criar uma VM do Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Selecione um tamanho para a VM. Pode filtrar por **Tipo de computação** ou **Tipo de disco**, por exemplo. 

    ![Tamanho da máquina virtual-visão geral do agente Telegraph](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Na página **configurações** , em > **grupo** > de segurança de rede de rede,**selecione portas de entrada públicas**, selecione **http** e **SSH (22)** . Deixe o resto das predefinições e selecione **OK**. 

1. Na página de resumo, selecione **Criar** para iniciar a implementação da VM. 

1. A VM é afixada ao dashboard do portal do Azure. Após a conclusão da implantação, o resumo da VM é aberto automaticamente. 

1. No painel VM, navegue até a guia **identidade** . Verifique se sua VM tem uma identidade atribuída pelo sistema definida como **ativada**. 
 
    ![Visualização de identidade de VM Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Ligar à VM 

Crie uma ligação SSH à VM. Selecione o botão **Ligar** na página de descrição geral da VM. 

![Página Visão geral da VM Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Na página **Ligar à máquina virtual**, mantenha as opções predefinidas para estabelecer ligação por nome DNS através da porta 22. Em **logon usando a conta local da VM**, é mostrado um comando de conexão. Selecione o botão para copiar o comando. O exemplo seguinte mostra o aspeto do comando de ligação SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Cole o comando de conexão SSH em um shell, como Azure Cloud Shell ou bash no Ubuntu no Windows, ou use um cliente SSH de sua escolha para criar a conexão. 

## <a name="install-and-configure-telegraf"></a>Instalar e configurar o Telegraf 

Para instalar o pacote Telegraf Debian na VM, execute os seguintes comandos na sua sessão SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
O arquivo de configuração do Telegraf define as operações do Telegraf. Por padrão, um arquivo de configuração de exemplo é instalado no caminho **/etc/Telegraf/Telegraf.conf**. O arquivo de configuração de exemplo lista todos os plug-ins de entrada e saída possíveis. No entanto, vamos criar um arquivo de configuração personalizado e fazer com que o agente o use, executando os seguintes comandos: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> O código anterior permite apenas dois plug-ins de entrada: **CPU** e **mem**. Você pode adicionar mais plug-ins de entrada, dependendo da carga de trabalho que é executada em seu computador. Os exemplos são Docker, MySQL e NGINX. Para obter uma lista completa de plug-ins de entrada, consulte a seção **configuração adicional** . 

Por fim, para que o agente comece a usar a nova configuração, forçamos o agente a parar e iniciar executando os seguintes comandos: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agora, o agente coletará as métricas de cada um dos plug-ins de entrada especificados e os emitirá para Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Plote suas métricas do Telegraf no portal do Azure 

1. Abra o [Portal do Azure](https://portal.azure.com). 

1. Navegue até a nova guia **Monitor** . Em seguida, selecione métricas.  

     ![Monitor-métricas (visualização)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Selecione sua VM no seletor de recursos.

     ![Gráfico de métrica](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Selecione o namespace **Telegraf/CPU** e selecione a métrica **usage_system** . Você pode optar por filtrar pelas dimensões nessa métrica ou dividi-las.  

     ![Selecionar o namespace e a métrica](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Configuração adicional 

O guia de explicação anterior fornece informações sobre como configurar o agente Telegraf para coletar métricas de alguns plug-ins de entrada básicos. O agente Telegraf tem suporte para mais de 150 plug-ins de entrada, com algumas opções de configuração adicionais de suporte. O InfluxData publicou uma [lista de plug-ins com suporte](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) e instruções sobre [como configurá-los](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Além disso, neste passo a passo, você usou o agente Telegraf para emitir métricas sobre a VM na qual o agente está implantado. O agente Telegraf também pode ser usado como coletor e encaminhador de métricas para outros recursos. Para saber como configurar o agente para emitir métricas para outros recursos do Azure, consulte [Azure monitor saída de métrica personalizada para Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Limpar recursos 

Quando eles não forem mais necessários, você poderá excluir o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos para a máquina virtual e selecione **excluir**. Em seguida, confirme o nome do grupo de recursos a ser excluído. 

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).



