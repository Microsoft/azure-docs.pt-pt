---
title: Recolher métricas personalizadas para Linux VM com o agente InfluxData Telegraf
description: Instruções sobre como implantar o agente InfluxData Telegraf num VM Linux em Azure e configurar o agente para publicar métricas no Monitor Azure.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 0ed9144116c1d716124025ef0aae39e7783c5934
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655468"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Colete métricas personalizadas para um VM Linux com o agente InfluxData Telegraf

Ao utilizar o Monitor Azure, pode recolher métricas personalizadas através da sua telemetria de aplicações, um agente que executa os seus recursos Azure ou até mesmo sistemas de monitorização externos. Depois pode submetê-los diretamente ao Monitor Azure. Este artigo fornece instruções sobre como implantar o agente [InfluxData](https://www.influxdata.com/) Telegraf num VM Linux em Azure e configurar o agente para publicar métricas no Monitor Azure. 

## <a name="influxdata-telegraf-agent"></a>Agente Da Telegraf InfluxData 

[A Telegraf](https://docs.influxdata.com/telegraf/) é um agente plug-in que permite a recolha de métricas de mais de 150 fontes diferentes. Dependendo do que as cargas de trabalho funcionam no seu VM, pode configurar o agente para alavancar plug-ins de entrada especializado para recolher métricas. Exemplos são MySQL, NGINX e Apache. Ao utilizar plug-ins de saída, o agente pode então escrever para destinos que escolher. O agente Telegraf integrou diretamente com as métricas personalizadas do Monitor Azure REST API. Suporta um plug-in de saída do Monitor Azure. Ao utilizar este plug-in, o agente pode recolher métricas específicas da carga de trabalho no seu VM Linux e submetê-las como métricas personalizadas ao Monitor Azure. 

 ![Visão geral do agente telégrafo](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas 

Para este tutorial, implementamos um VM Linux que executa o sistema operativo Ubuntu 16.04 LTS. O agente Telegraf é suportado para a maioria dos sistemas operativos Linux. Tanto os pacotes Debian como rPM estão disponíveis juntamente com binários Linux não embalados no portal de [descarregamento InfluxData](https://portal.influxdata.com/downloads). Consulte este guia de [instalação da Telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) para obter instruções e opções adicionais de instalação. 

Inicie sessão no [Portal do Azure](https://portal.azure.com).

Crie um novo VM Linux: 

1. Selecione a opção **Criar uma** opção de recurso a partir do painel de navegação à esquerda. 
1. Pesquisa por **Máquina Virtual**.  
1. Selecione **Ubuntu 16.04 LTS** e selecione **Criar**. 
1. Forneça um nome VM como **MyTelegrafVM**.  
1. Deixe o tipo de disco como **SSD**. Em seguida, forneça um **nome de utilizador**, como o **azureuser**. 
1. Para **o tipo de autenticação,** selecione **Password**. Em seguida, introduza uma palavra-passe que utilizará mais tarde para sSH neste VM. 
1. Opte por **criar um novo grupo de recursos.** Em seguida, forneça um nome, como **o myResourceGroup**. Escolha a sua **localização**. Em seguida, selecione **OK**. 

    ![Criar uma VM do Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Selecione um tamanho para a VM. Pode filtrar por **Tipo de computação** ou **Tipo de disco**, por exemplo. 

    ![Visão geral do agente telegráfico tamanho da máquina virtual](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Na página **Definições** do**Grupo** > de Segurança da **Rede** > **Selecione portas de entrada públicas,** selecione **HTTP** e **SSH (22)**. Deixe o resto das predefinições e selecione **OK**. 

1. Na página de resumo, selecione **Criar** para iniciar a implementação da VM. 

1. A VM é afixada ao dashboard do portal do Azure. Após o acabamento da implantação, o resumo vM abre automaticamente. 

1. No painel VM, navegue para o separador **Identidade.** Certifique-se de que o seu VM tem uma identidade atribuída ao sistema definida em **On**. 
 
    ![Pré-visualização de identidade Telegraf VM](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Ligar à VM 

Crie uma ligação SSH à VM. Selecione o botão **Ligar** na página de descrição geral da VM. 

![Página geral do Telegraf VM](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Na página **Ligar à máquina virtual**, mantenha as opções predefinidas para estabelecer ligação por nome DNS através da porta 22. Em **Login utilizando a conta local VM,** é mostrado um comando de ligação. Selecione o botão para copiar o comando. O exemplo seguinte mostra o aspeto do comando de ligação SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Colar o comando de ligação SSH numa concha, como Azure Cloud Shell ou Bash em Ubuntu no Windows, ou usar um cliente SSH à sua escolha para criar a ligação. 

## <a name="install-and-configure-telegraf"></a>Instalar e configurar a Telegraf 

Para instalar o pacote Telegraf Debian no VM, execute os seguintes comandos da sua sessão SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
O ficheiro de configuração da Telegraf define as operações da Telegraf. Por predefinição, um ficheiro de configuração de exemplo é instalado no caminho **/etc/telegraf/telegraf.conf**. O ficheiro de configuração do exemplo lista todos os possíveis plug-ins de entrada e saída. No entanto, criaremos um ficheiro de configuração personalizado e faremos com que o agente o utilize executando os seguintes comandos: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> O código anterior permite apenas dois plug-ins de entrada: **cpu** e **mem**. Pode adicionar mais plug-ins de entrada, dependendo da carga de trabalho que funciona na sua máquina. Exemplos são Docker, MySQL e NGINX. Para obter uma lista completa de plug-ins de entrada, consulte a secção de **configuração Adicional.** 

Finalmente, para que o agente comece a usar a nova configuração, forçamos o agente a parar e começar por executar os seguintes comandos: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agora, o agente recolherá métricas de cada uma das fichas de entrada especificadas e emite-as para o Monitor Azure. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Planeie as suas métricas Telegraf no portal Azure 

1. Abra o [portal Azure.](https://portal.azure.com) 

1. Navegue para o novo separador **Monitor.** Em seguida, selecione **Métricas**.  

     ![Monitor - Métricas (pré-visualização)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Selecione o seu VM no seletor de recursos.

     ![Gráfico métrico](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Selecione o espaço de nome **Telegraf/CPU** e selecione a métrica **usage_system.** Pode optar por filtrar pelas dimensões desta métrica ou dividir-se nelas.  

     ![Selecione espaço de nome e métrica](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Configuração adicional 

O walkthrough anterior fornece informações sobre como configurar o agente Telegraf para recolher métricas de alguns plug-ins básicos de entrada. O agente Telegraf tem suporte para mais de 150 plug-ins de entrada, com algumas opções de configuração adicionais de suporte. A InfluxData publicou uma [lista de plugins e](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) instruções suportadas sobre como [configurá-los](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Além disso, neste passeio, usou o agente Telegraf para emitir métricas sobre o VM em que o agente está implantado. O agente Telegraf também pode ser usado como colecionador e reencaminhador de métricas para outros recursos. Para aprender a configurar o agente para emitir métricas para outros recursos Azure, consulte [a Saída Métrica Personalizada do Monitor Azure para a Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Limpar recursos 

Quando já não são necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para isso, selecione o grupo de recursos para a máquina virtual e selecione **Eliminar**. Em seguida, confirme o nome do grupo de recursos para apagar. 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](metrics-custom-overview.md)



