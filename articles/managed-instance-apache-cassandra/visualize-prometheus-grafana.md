---
title: Configure Grafana para visualizar métricas emitidas de Azure Managed Instance para Apache Cassandra
description: Aprenda a instalar e configurar Grafana num VM para visualizar as métricas emitidas a partir de um aglomerado gerido por Azure Para o aglomerado Apache Cassandra.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746588"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Configure Grafana para visualizar as métricas emitidas a partir do cluster de instâncias geridas

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando implementa uma Instância Gerida Azure para o cluster Apache Cassandra, o serviço fornece um servidor que hospeda [a Prometeu](https://prometheus.io/) que pode ser consumido por várias ferramentas do cliente. Prometheus é uma solução de monitorização de código aberto. A instância gerida emitirá métricas e retém 10 minutos ou 10 GB de dados (qualquer que seja o limiar que atinja primeiro). Este artigo descreve como configurar Grafana para visualizar as métricas emitidas a partir do cluster de instâncias geridas. São necessárias as seguintes tarefas para visualizar métricas:

* Implementar uma Máquina Virtual Ubuntu dentro da Rede Virtual Azure onde a instância gerida está presente.
* Instale a [ferramenta Grafana](https://grafana.com/grafana/) de código aberto para construir painéis de instrumentos e visualizar as métricas emitidas pela Prometeu.

## <a name="deploy-a-ubuntu-server"></a>Implementar um servidor Ubuntu

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue para o grupo de recursos onde o seu cluster de instância gerido está localizado. **Selecione Adicionar** e procurar a imagem **do Ubuntu Server 18.04 LTS:**

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Encontre a imagem do servidor Ubuntu a partir do portal Azure." border="true":::

1. Escolha a imagem e selecione **Criar**.

1. Na lâmina de **máquina virtual,** introduza valores para os seguintes campos, pode deixar valores predefinidos para outros campos:

   * **Nome da máquina virtual** - Introduza um nome para si VM.
   * **Região** - Selecione a mesma região onde a sua Rede Virtual foi implantada.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Preencha o formulário para criar um VM com a imagem do servidor Ubuntu." border="true":::

1. No **separador 'Rede',** selecione a Rede Virtual na qual a sua instância gerida é implementada:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Configure as definições de rede do servidor Ubuntu." border="true":::

1. Finalmente selecione **Review + Create** para criar o seu servidor Grafana.

## <a name="install-grafana"></a>Instalar Grafana

1. A partir do portal Azure, abra a Rede Virtual onde implementou a instância gerida e o Servidor Grafana. Deve ver uma instância de escala de máquina virtual chamada **cassandra-jump (exemplo 0)**. Estas métricas Prometheus estão hospedadas neste conjunto de escala de máquina virtual. Tome nota do endereço IP deste caso:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Obtenha o endereço IP da instância Prometheus." border="true":::

1. Ligue-se ao seu servidor Ubuntu recém-criado utilizando [o Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) ou a sua ferramenta cliente preferida para ligar via SSH.

1. Depois de ligar ao VM, tem de instalar e configurar grafana para ligar ao conjunto de balanças de máquinas virtuais onde as métricas estão hospedadas. Abra um pedido de comando e insira o `nano` comando para abrir um editor de texto Nano. Cole o seguinte script no editor de texto, certifique-se de substituir o `<prometheus IP address>` endereço IP que gravou no passo anterior:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. Escreva `ctrl + X` para guardar o ficheiro. Pode nomear o `grafana.sh` ficheiro.

1. Introduza o `./grafana.sh` comando no comando para instalar Grafana.

1. Após a conclusão da instalação, a Grafana estará disponível na **porta 3000** no endereço IP do servidor, tal como mostrado na seguinte imagem:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Corra Grafana no porto 3000." border="true":::

1. Pode escolher entre dashboards de código aberto criados para Apache Cassandra em Grafana, como o ficheiro JSON [de visão geral](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) do cluster. Descarregue e importe a definição JSON do dashboard em Grafana:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Import Grafana JSON definição." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Carre faça upload da definição de Grafana JSON." border="true":::

1. Em seguida, pode monitorizar o seu agrupamento de instância gerido por Cassandra com o painel de instrumentos escolhido:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Veja as métricas de exemplo geridas por Cassandra no painel de instrumentos." border="true":::

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar dashboards para visualizar métricas em Prometeu usando Grafana. Saiba mais sobre a Azure Managed Instance for Apache Cassandra com os seguintes artigos:

* [Visão geral de Azure Managed Instance para Apache Cassandra](introduction.md)
* [Implementar um Cluster de Faíscas Apache Gerido com Tijolos de Dados Azure (Pré-visualização)](deploy-cluster-databricks.md)
