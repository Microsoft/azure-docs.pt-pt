---
title: Publicar aplicações Azure HDInsight
description: Aprenda a criar uma aplicação HDInsight e, em seguida, publique-a no Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: ca84cb6cdd6b47976eadbc5298701a46fe677426
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547831"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publicar uma aplicação HDInsight no Azure Marketplace
Pode instalar uma aplicação Azure HDInsight num cluster HDInsight baseado em Linux. Neste artigo, aprenda a publicar uma aplicação HDInsight no Azure Marketplace. Para obter informações gerais sobre a publicação no Azure Marketplace, consulte [publicar uma oferta no Azure Marketplace](../marketplace/overview.md).

As aplicações HDInsight utilizam o modelo *Bring Your Own License (BYOL).* Num cenário BYOL, um fornecedor de aplicações é responsável pelo licenciamento da aplicação aos utilizadores de aplicações. Os utilizadores de aplicações são cobrados apenas pelos recursos Azure que criam, como o cluster HDInsight, e os VMs e nós do cluster. Atualmente, a faturação da aplicação em si não ocorre em Azure.

Para obter mais informações, consulte estes artigos relacionados com aplicações HDInsight:

* [Instale aplicações HDInsight](hdinsight-apps-install-applications.md). Saiba como instalar uma aplicação HDInsight nos seus clusters.
* [Instale aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md). Saiba como instalar e testar aplicações HDInsight personalizadas.

## <a name="prerequisites"></a>Pré-requisitos
Para submeter a sua aplicação personalizada no Marketplace, em primeiro lugar, [crie e teste a sua aplicação personalizada.](hdinsight-apps-install-custom-applications.md)

Também tem de registar a sua conta de programador. Para obter mais informações, consulte [publicar uma oferta no Azure Marketplace](../marketplace/overview.md) e criar uma conta de Desenvolvedor da [Microsoft.](../marketplace/overview.md)

## <a name="define-the-application"></a>Definir a aplicação
Estão envolvidos dois passos na publicação de aplicações no Mercado. Primeiro, defina um *createUiDef.jsarquivado.* O createUiDef.jsno ficheiro indica com que agrupamentos a sua aplicação é compatível. Em seguida, publique o modelo a partir do portal Azure. Aqui está uma amostra createUiDef.jsarquivada:

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

## <a name="application-installation-script"></a>Script de instalação de aplicação
Quando uma aplicação é instalada num cluster (seja num cluster existente, ou num novo), é criado um nó de borda. O script de instalação da aplicação funciona no nó de borda.

  > [!IMPORTANT]  
  > O nome do script de instalação da aplicação deve ser único para um cluster específico. O nome do guião deve ter o seguinte formato:
  > 
  > "Nome": "[concat'hue-install-v0','',', unstring ('applicationName']]"
  > 
  > O nome do guião tem três partes:
  > 
  > * Um prefixo de nome de guião, que deve incluir o nome da aplicação ou um nome relevante para a aplicação.
  > * Um hífen, para a legibilidade.
  > * Uma função de corda única, com o nome da aplicação como parâmetro.
  > 
  > Na lista de ação do script persistido, o exemplo anterior é apresentado como **hue-install-v0-4wkahss5hlas** . Consulte uma [amostra da carga útil do JSON.](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json)
  > 

O roteiro de instalação deve ter as seguintes características:
* O guião é idempotente. Várias chamadas para o script produzem o mesmo resultado.
* O script está devidamente versado. Utilize um local diferente para o script quando estiver a atualizar ou a testar alterações. Isto garante que os clientes que estão a instalar a aplicação não são afetados pelas suas atualizações ou testes. 
* O script tem registos adequados em cada ponto. Normalmente, os registos de scripts são a única forma de depurar problemas de instalação de aplicações.
* As chamadas para serviços ou recursos externos têm retrações adequadas para que a instalação não seja afetada por problemas transitórios de rede.
* Se o seu script iniciar serviços nos nós, os serviços são monitorizados e configurados para iniciar automaticamente se ocorrer um reboot de nó.

## <a name="package-the-application"></a>Embale a aplicação
Crie um ficheiro .zip que contenha todos os ficheiros necessários para instalar a sua aplicação HDInsight. Utilize o ficheiro .zip para publicar a aplicação. O ficheiro .zip inclui os seguintes ficheiros:

* createUiDefinition.jsem
* mainTemplate.js(Para uma amostra, consulte [instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).)
* Todos os scripts necessários

> [!NOTE]  
> Pode hospedar os ficheiros de aplicações (incluindo quaisquer ficheiros de aplicações web) em qualquer ponto final acessível ao público.

## <a name="publish-the-application"></a>Publicar a aplicação
Para publicar uma aplicação HDInsight:

1. Inscreva-se na [Azure Publishing](https://publish.windowsazure.com/).
2. No menu esquerdo, **selecione Modelos de solução** .
3. Introduza um título e, em seguida, **selecione Criar um novo modelo de solução** .
4. Se ainda não registou a sua organização, selecione **a conta Create Dev Center e junte-se ao programa Azure.**  Para obter mais informações, consulte [Criar uma conta microsoft developer](../marketplace/overview.md).
5. Selecione **Definir algumas Topologias para começar** . Um modelo de solução é um "pai" para todas as suas topologias. Pode definir várias topologias num modelo de oferta ou solução. Quando uma oferta é empurrada para a encenação, é empurrada com todas as suas topologias. 
6. Introduza um nome de topologia e, em seguida, selecione **+** .
7. Introduza uma nova versão e, em seguida, selecione **+** .
8. Faça o upload do ficheiro .zip que criou quando embalou a aplicação.  
9. Selecione **Certificação de Pedidos.** A equipa de certificação da Microsoft analisa os ficheiros e certifica a topologia.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [instalar aplicações HDInsight](hdinsight-apps-install-applications.md) nos seus clusters.
* Aprenda a [instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md) e implemente uma aplicação HDInsight não publicada para o HDInsight.
* Aprenda a usar a [Script Action para personalizar clusters HDInsight baseados em Linux](hdinsight-hadoop-customize-cluster-linux.md) e adicionar mais aplicações. 
* Aprenda a [criar clusters Apache Hadoop baseados em Linux em HDInsight utilizando modelos de Gestor de Recursos Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Aprenda a [usar um nó de borda vazia em HDInsight](hdinsight-apps-use-edge-node.md) para aceder a clusters HDInsight, testar aplicações HDInsight e apresentar aplicações HDInsight.