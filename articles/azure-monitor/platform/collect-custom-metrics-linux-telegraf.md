---
title: Recolher métricas personalizadas para uma VM do Linux com o agente de InfluxData Telegraf
description: Recolher métricas personalizadas para uma VM do Linux com o agente de InfluxData Telegraf
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 14415b88cd6036642442ef9ae23e8dee301bb908
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775616"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Recolher métricas personalizadas para uma VM do Linux com o agente de InfluxData Telegraf

Ao utilizar o Azure Monitor, pode recolher métricas personalizadas através de telemetria da sua aplicação, um agente em execução em seus recursos do Azure, ou até mesmo fora para dentro de sistemas de monitorização. Em seguida, pode enviá-los diretamente para o Azure Monitor. Este artigo fornece instruções sobre como implementar o [InfluxData](https://www.influxdata.com/) Telegraf agente numa VM do Linux no Azure e configurar o agente para publicar as métricas para o Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>Agente de InfluxData Telegraf 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) é um agente de plug-in controlado por mensagens em fila que permite a recolha de métricas mais de 150 origens diferentes. Consoante o que as cargas de trabalho executam na sua VM, pode configurar o agente para tirar partido especializadas entradas plug-ins para recolher métricas. Os exemplos são Apache, MySQL e NGINX. Ao utilizar o plug-ins de saída, o agente, em seguida, pode escrever para destinos que escolher. O agente de Telegraf integrou-se diretamente com as métricas personalizadas do Azure Monitor REST API. Ele oferece suporte uma saída de Azure Monitor Plug-in. Com esta Plug-in, o agente pode recolher métricas de carga de trabalho específica na sua VM do Linux e enviá-los métricas como personalizadas para o Azure Monitor. 

 ![Descrição geral do agente telégrafo](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas 

Para este tutorial, vamos implementar uma VM do Linux que executa o sistema de operativo Ubuntu 16.04 LTS. O agente de Telegraf é suportado para a maioria dos sistemas de operativos Linux. Pacotes Debian e RPM estão disponíveis, juntamente com descompactadas Linux os binários dos [portal de transferência de InfluxData](https://portal.influxdata.com/downloads). Ver isso [guia de instalação Telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) para instruções de instalação adicionais e opções. 

Inicie sessão no [portal do Azure](https://portal.azure.com).

Crie uma nova VM do Linux: 

1. Selecione o **criar um recurso** opção no painel de navegação esquerdo. 
1. Procure **Máquina Virtual**.  
1. Selecione **Ubuntu 16.04 LTS** e selecione **criar**. 
1. Forneça um nome VM, como **MyTelegrafVM**.  
1. Deixe o tipo de disco como **SSD**. Em seguida, forneça uma **nome de utilizador**, tal como **azureuser**. 
1. Para **tipo de autenticação**, selecione **palavra-passe**. Em seguida, introduza uma palavra-passe que irá utilizar mais tarde para encaminhar o SSH para esta VM. 
1. Optar por **criar novo grupo de recursos**. Em seguida, forneça um nome, tal como **myResourceGroup**. Escolha sua **localização**. Em seguida, selecione **OK**. 

    ![Criar uma VM do Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Selecione um tamanho para a VM. Pode filtrar por **tipo de computação** ou **tipo de disco**, por exemplo. 

    ![Descrição geral do agente de telégrafo de tamanho do máquina virtual](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Sobre o **definições** página no **rede** > **grupo de segurança de rede**   >  ** Selecione as portas de entrada públicas**, selecione **HTTP** e **SSH (22)**. Deixe o resto dos padrões e selecione **OK**. 

1. Na página Resumo, selecione **Create** para iniciar a implementação de VM. 

1. A VM é afixada ao dashboard do portal do Azure. Após a conclusão da implementação, o resumo da VM abre automaticamente. 

1. No painel da VM, navegue para o **identidade** separador. Certifique-se de que a VM tem uma identidade atribuída por sistema definida como **no**. 
 
    ![Pré-visualização da identidade de VMS de Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Ligar à VM 

Crie uma ligação SSH à VM. Selecione o **Connect** botão na página de descrição geral para a sua VM. 

![Página de descrição geral de Telegraf VM](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Na **ligar à máquina virtual** página, mantenha as opções predefinidas para se conectar por nome DNS através da porta 22. Na **início de sessão com a conta local de VM**, um comando de ligação é mostrado. Selecione o botão para copiar o comando. O exemplo seguinte mostra o aspeto do comando de ligação SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Cole o comando de ligação de SSH para uma shell, tais como o Azure Cloud Shell ou Bash no Ubuntu no Windows, ou utilizar um cliente SSH à sua escolha para criar a ligação. 

## <a name="install-and-configure-telegraf"></a>Instalar e configurar Telegraf 

Para instalar o pacote Debian Telegraf na VM, execute os seguintes comandos da sua sessão SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Ficheiro de configuração do Telegraf define as operações do Telegraf. Por predefinição, um arquivo de configuração de exemplo está instalado no caminho **/etc/telegraf/telegraf.conf**. O ficheiro de configuração de exemplo lista todas as entradas de possíveis e plug-ins de saída. No entanto, vamos criar um ficheiro de configuração personalizada e têm o agente de usá-lo ao executar os comandos seguintes: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> O código anterior permite que apenas dois plug-ins de entrada: **cpu** e **memória**. Pode adicionar mais entradas plug-ins, dependendo da carga de trabalho que é executado no seu computador. Os exemplos são o Docker, o MySQL e o NGINX. Para obter uma lista completa de entrada de plug-ins, consulte a **configuração adicional** secção. 

Por fim, para que o início de agente utilizando a nova configuração, podemos forçar o agente para parar e iniciar ao executar os comandos seguintes: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agora, o agente irá recolher métricas de cada um dos plug-ins entrados que especificou e emiti-los para o Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Desenhar as suas métricas Telegraf no portal do Azure 

1. Abra o [Portal do Azure](https://portal.azure.com). 

1. Navegue para a nova **Monitor** separador. Em seguida, selecione **métricas**.  

     ![Monitorizar - métricas (pré-visualização)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Selecione a sua VM no Seletor de recursos.

     ![Gráfico de métricas](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Selecione o **Telegraf/CPU** espaço de nomes e selecione o **usage_system** métrica. Pode optar por filtrar por dimensões sobre esta métrica ou divisão nos mesmos.  

     ![Selecione o espaço de nomes e métrica](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Configuração adicional 

O passo a passo anterior fornece informações sobre como configurar o agente de Telegraf para recolher métricas de alguns básicas entradas plug-ins. O agente de Telegraf tem suporte para mais de 150 entradas plug-ins, com algumas opções de configuração adicionais de suporte. InfluxData publicou um [lista de plug-ins suportados](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) e instruções sobre [como configurá-las](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Além disso, nestas instruções, utilizou o agente de Telegraf para emitir métricas sobre a VM, o agente é implementado no. O agente de Telegraf também pode ser utilizado como um recoletor e o reencaminhador de métricas para outros recursos. Para saber como configurar o agente para emitir métricas para outros recursos do Azure, veja [saída de métrica do Azure Monitor personalizado para Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Limpar recursos 

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual e selecione **eliminar**. Em seguida, confirme o nome do grupo de recursos para eliminar. 

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).



