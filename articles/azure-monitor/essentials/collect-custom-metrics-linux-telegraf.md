---
title: Colete métricas personalizadas para Linux VM com o agente InfluxData Telegraf
description: Instruções sobre como implantar o agente InfluxData Telegraf num Linux VM em Azure e configurar o agente para publicar métricas no Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 4274f976a04a9694c0429a2a4794d645304a9e97
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728812"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Colete métricas personalizadas para um Linux VM com o agente InfluxData Telegraf

Ao utilizar o Azure Monitor, pode recolher métricas personalizadas através da telemetria da aplicação, de um agente que executa os seus recursos Azure ou até mesmo de sistemas de monitorização externos. Em seguida, pode submetê-los diretamente ao Monitor Azure. Este artigo fornece instruções sobre como implantar o agente [InfluxData](https://www.influxdata.com/) Telegraf num Linux VM em Azure e configurar o agente para publicar métricas para o Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>Agente Da Telegraf InfluxData 

[A Telegraf](https://docs.influxdata.com/telegraf/) é um agente plug-in que permite a recolha de métricas de mais de 150 fontes diferentes. Dependendo das cargas de trabalho executadas no seu VM, pode configurar o agente para alavancar os plug-ins de entrada especializados para recolher métricas. Exemplos são MySQL, NGINX e Apache. Ao utilizar plug-ins de saída, o agente pode escrever para destinos que escolher. O agente da Telegraf integrou-se diretamente com as métricas personalizadas do Azure Monitor REST API. Suporta um plug-in de saída do Azure Monitor. Ao utilizar este plug-in, o agente pode recolher métricas específicas da carga de trabalho no seu Linux VM e submetê-las como métricas personalizadas ao Azure Monitor. 

 ![Visão geral do agente de telégrafo](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> As métricas personalizadas não são suportadas em todas as regiões. Regiões apoiadas estão listadas [aqui](./metrics-custom-overview.md#supported-regions)

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas 

Para este tutorial, implementamos um Linux VM que executa o sistema operativo Ubuntu 16.04 LTS. O agente Telegraf é suportado para a maioria dos sistemas operativos Linux. Tanto os pacotes Debian como RPM estão disponíveis juntamente com binaries Linux não embalados no [portal de descarregamento do InfluxData](https://portal.influxdata.com/downloads). Consulte este [guia de instalação da Telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) para obter instruções e opções de instalação adicionais. 

Inicie sessão no [portal do Azure](https://portal.azure.com).

> [!NOTE]  
> Se pretender migrar regras clássicas de alerta e utilizar uma máquina virtual Linux existente, certifique-se de que a máquina virutal tem uma identidade atribuída ao sistema definida para **On**.

Criar um novo Linux VM: 

1. Selecione a opção **de recurso a** partir do painel de navegação à esquerda. 
1. Procure por **Máquina Virtual.**  
1. Selecione **Ubuntu 16.04 LTS** e selecione **Criar**. 
1. Forneça um nome VM como **MyTelegrafVM**.  
1. Deixe o tipo de disco como **SSD**. Em seguida, forneça um nome de **utilizador**, como **azureuser**. 
1. Para **o tipo de autenticação**, selecione **Password**. Em seguida, introduza uma palavra-passe que usará mais tarde para SSH neste VM. 
1. Opte por **criar um novo grupo de recursos.** Em seguida, forneça um nome, como **o myResourceGroup**. Escolha a sua **localização.** Em seguida, selecione **OK**. 

    ![Criar uma VM do Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Selecione um tamanho para a VM. Pode filtrar por **Tipo de computação** ou **Tipo de disco**, por exemplo. 

    ![Visão geral do agente de telégrafo do tamanho da máquina virtual](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Na página **Definições** do Grupo de Segurança da Rede De **Rede**  >    >  **Selecione as portas de entrada pública**, selecione **HTTP** e **SSH (22)**. Deixe o resto das predefinições e selecione **OK**. 

1. Na página de resumo, selecione **Criar** para iniciar a implementação da VM. 

1. A VM é afixada ao dashboard do portal do Azure. Após o fim da colocação, o resumo VM abre-se automaticamente. 

1. No painel VM, navegue para o **separador Identidade.** Certifique-se de que o seu VM tem uma identidade atribuída ao sistema definida para **On**. 
 
    ![Pré-visualização da identidade da Telegraf VM](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Ligar à VM 

Crie uma ligação SSH à VM. Selecione o botão **Ligar** na página de descrição geral da VM. 

![Página geral do Telegraf VM](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Na página **Ligar à máquina virtual**, mantenha as opções predefinidas para estabelecer ligação por nome DNS através da porta 22. No **Login utilizando a conta local VM,** é mostrado um comando de ligação. Selecione o botão para copiar o comando. O exemplo seguinte mostra o aspeto do comando de ligação SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Cole o comando de ligação SSH numa concha, como a Azure Cloud Shell ou a Bash on Ubuntu no Windows, ou utilize um cliente SSH à sua escolha para criar a ligação. 

## <a name="install-and-configure-telegraf"></a>Instalar e configurar a Telegraf 

Para instalar o pacote Telegraf Debian no VM, execute os seguintes comandos a partir da sua sessão SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
O ficheiro de configuração da Telegraf define as operações da Telegraf. Por predefinição, um ficheiro de configuração de exemplo é instalado no caminho **/etc/telegraf/telegraf.conf**. O ficheiro de configuração de exemplo lista todos os possíveis plug-ins de entrada e saída. No entanto, criaremos um ficheiro de configuração personalizado e faremos com que o agente o utilize executando os seguintes comandos: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> O código anterior permite apenas dois plug-ins de entrada: **cpu** e **mem**. Pode adicionar mais plug-ins de entrada, dependendo da carga de trabalho que funciona na sua máquina. Exemplos são Docker, MySQL e NGINX. Para obter uma lista completa de plug-ins de entrada, consulte a secção **de configuração adicional.** 

Finalmente, para que o agente comece a usar a nova configuração, forçamos o agente a parar e começar a executar os seguintes comandos: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agora, o agente recolherá métricas de cada um dos plug-ins de entrada especificados e emite-as ao Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Defina as suas métricas Telegraf no portal Azure 

1. Abra o [portal do Azure](https://portal.azure.com). 

1. Navegue para o novo **separador Monitor.** Em seguida, selecione **Métricas**.  

     ![Monitor - Métricas (pré-visualização)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Selecione o seu VM no seletor de recursos.

     ![Gráfico métrico](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Selecione o **espaço de nomes Telegraf/CPU** e selecione a métrica **usage_system.** Pode optar por filtrar pelas dimensões desta métrica ou dividir-se sobre elas.  

     ![Selecione espaço de nome e métrica](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Configuração adicional 

O walkthrough anterior fornece informações sobre como configurar o agente Telegraf para recolher métricas de alguns plug-ins básicos de entrada. O agente Telegraf tem suporte para mais de 150 plug-ins de entrada, com algumas opções de configuração adicionais de suporte. O InfluxData publicou uma [lista de plugins suportados](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/) e instruções sobre [como configurá-los](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/).  

Além disso, nesta passagem, usou o agente Telegraf para emitir métricas sobre o VM em que o agente é implantado. O agente Telegraf também pode ser usado como colecionador e reencaminhador de métricas para outros recursos. Para aprender a configurar o agente para emitir métricas para outros recursos Azure, consulte [a Produção Métrica Personalizada do Monitor Azure para a Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Limpar os recursos 

Quando já não são necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual e selecione **Delete**. Em seguida, confirme o nome do grupo de recursos para apagar. 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](./metrics-custom-overview.md)