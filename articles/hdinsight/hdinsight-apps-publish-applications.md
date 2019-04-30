---
title: Publicar aplicações do Azure HDInsight
description: Saiba como criar uma aplicação do HDInsight e, em seguida, publicá-la no Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e64bf253a73df3a2f8170109dc1dfb9a59613733
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097407"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publicar uma aplicação de HDInsight no Azure Marketplace
Pode instalar uma aplicação do Azure HDInsight num cluster do HDInsight baseado em Linux. Neste artigo, saiba como publicar uma aplicação do HDInsight no Azure Marketplace. Para obter informações gerais sobre a publicação no Azure Marketplace, consulte [publicar uma oferta no Azure Marketplace](../marketplace/marketplace-publishers-guide.md).

Aplicações do HDInsight utilizam o *traga a sua própria licença (BYOL)* modelo. Num cenário BYOL, um provedor de aplicação é responsável por licenciar a aplicação para utilizadores da aplicação. Os utilizadores da aplicação cobrados apenas pelos recursos do Azure que criaram, como o cluster do HDInsight, o cluster, VMs e nós. Atualmente, a faturação da própria aplicação não ocorre no Azure.

Para obter mais informações, consulte estes artigos relacionados com a aplicação do HDInsight:

* [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md). Saiba como instalar uma aplicação HDInsight nos seus clusters.
* [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md). Saiba como instalar e testar aplicações do HDInsight personalizadas.

## <a name="prerequisites"></a>Pré-requisitos
Para submeter a sua aplicação personalizada no Marketplace, em primeiro lugar, [criar e testar a sua aplicação personalizada](hdinsight-apps-install-custom-applications.md).

Também deve registrar sua conta de programador. Para obter mais informações, consulte [publicar uma oferta no Azure Marketplace](../marketplace/marketplace-publishers-guide.md) e [criar uma conta do Microsoft Developer](../marketplace/marketplace-publishers-guide.md).

## <a name="define-the-application"></a>Definir a aplicação
Duas etapas envolvidas na publicação de aplicações no Marketplace. Primeiro, define um *Createuidef* ficheiro. O ficheiro Createuidef indica quais grupos o seu aplicativo é compatível com. Em seguida, publique o modelo a partir do portal do Azure. Este é um ficheiro Createuidef de exemplo:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Campo | Descrição | Valores possíveis |
| --- | --- | --- |
| tipos |Os tipos de cluster com os quais a aplicação é compatível. |Hadoop, HBase, Storm, Spark (ou qualquer combinação destes) |
| versões |Os tipos de cluster do HDInsight com os quais a aplicação é compatível. |3.4 |

## <a name="application-installation-script"></a>Script de instalação da aplicação
Quando uma aplicação é instalada num cluster (seja num cluster existente, ou num novo), é criado um nó de extremidade. O script de instalação do aplicativo é executado no nó de extremidade.

  > [!IMPORTANT]  
  > O nome do script de instalação de aplicação tem de ser exclusivo para um cluster específico. O nome do script tem de ter o seguinte formato:
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > O nome do script possui três partes:
  > 
  > * Um prefixo do nome do script, que tem de incluir o nome da aplicação ou um nome relevante para a aplicação.
  > * Um hífen, para facilitar a leitura.
  > * Uma função de cadeia de caracteres exclusivo, com o nome da aplicação como o parâmetro.
  > 
  > Na lista de ações de script persistentes, o exemplo anterior é apresentado como **hue-install-v0-4wkahss55hlas**. Veja uma [payload JSON de exemplo](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

O script de instalação tem de ter as seguintes características:
* O script é idempotent. Várias chamadas para o script de produzem o mesmo resultado.
* O script é corretamente com a versão. Utilize uma localização diferente para o script quando estiver a atualizar ou teste de alterações. Isto garante que os clientes que estão a instalar a aplicação não são afetadas pelas suas atualizações ou de teste. 
* O script tem o registo adequado em cada ponto. Normalmente, os logs de script são a única forma de depurar problemas de instalação de aplicativos.
* Chamadas para serviços externos ou recursos tem repetições adequadas para que a instalação não é afetada por problemas de rede transitórios.
* Se o seu script inicia os serviços em nós, os serviços são monitorizados e configurados para iniciar automaticamente se ocorrer um reinício do nó.

## <a name="package-the-application"></a>Empacotar a aplicação
Crie um ficheiro. zip que contém todos os ficheiros que são necessários para instalar a aplicação do HDInsight. Utilize o ficheiro. zip para publicar a aplicação. O ficheiro. zip inclui os seguintes ficheiros:

* createUiDefinition.json
* maintemplate. JSON (para obter um exemplo, veja [instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).)
* Scripts de contas necessários

> [!NOTE]  
> Pode alojar os ficheiros da aplicação (incluindo quaisquer ficheiros de aplicação web) em qualquer ponto final acessível publicamente.

## <a name="publish-the-application"></a>Publicar a aplicação
Para publicar uma aplicação do HDInsight:

1. Inicie sessão no [publicação no Azure](https://publish.windowsazure.com/).
2. No menu da esquerda, selecione **modelos de soluções**.
3. Introduza um título e, em seguida, selecione **criar um novo modelo de solução**.
4. Se ainda não registou sua organização, selecione **conta criar Dev Center e aderir ao programa Azure**.  Para obter mais informações, consulte [criar uma conta do Microsoft Developer](../marketplace/marketplace-publishers-guide.md).
5. Selecione **definir algumas topologias para começar a utilizar**. Um modelo de solução é um "elemento principal" de todas as respetivas topologias. Pode definir vários topologias num modelo de solução ou oferta. Quando uma oferta é emitida para teste, esta é instalada com todas as respetivas topologias. 
6. Introduza um nome de topologia e, em seguida, selecione **+**.
7. Introduza uma nova versão e, em seguida, selecione **+**.
8. Carregue o ficheiro. zip que criou quando empacotado que a aplicação.  
9. Selecione **solicitar a certificação**. A equipe de certificação da Microsoft analisa os ficheiros e certifica a topologia.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [instalar aplicações HDInsight](hdinsight-apps-install-applications.md) nos seus clusters.
* Saiba como [instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md) e implementar uma aplicação HDInsight não publicada para o HDInsight.
* Saiba como [utilize a ação de Script para personalizar os clusters do HDInsight baseado em Linux](hdinsight-hadoop-customize-cluster-linux.md) e adicionar mais aplicações. 
* Saiba como [criar clusters do Apache Hadoop baseado em Linux no HDInsight com modelos Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Saiba como [utilizar um nó de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md) para aceder a clusters do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.

