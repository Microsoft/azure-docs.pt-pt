---
title: Base de dados Personalizada Apache Ambari no Azure HDInsight
description: Aprenda a criar clusters HDInsight com a sua própria base de dados Personalizada Apache Ambari.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240161"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Configurar clusters HDInsight com um Ambari DB personalizado

Apache Ambari simplifica a gestão e monitorização de um cluster Apache Hadoop. Ambari fornece uma UI web fácil de usar e REST API. Ambari está incluído em clusters HDInsight, e é usado para monitorizar o cluster e fazer alterações de configuração.

Na criação normal de clusters, como descrito em outros artigos como [Configurar clusters no HDInsight,](hdinsight-hadoop-provision-linux-clusters.md)Ambari é implantado numa base de [dados S0 Azure SQL](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) que é gerida pelo HDInsight e não é acessível aos utilizadores.

A funcionalidade Ambari DB personalizada permite-lhe implementar um novo cluster e configurar ambari numa base de dados externa que gere. A implementação é feita com um modelo de Gestor de Recursos Azure. Esta funcionalidade tem os seguintes benefícios:

- Personalização - escolha o tamanho e a capacidade de processamento da base de dados. Se tiver grandes clusters a processar cargas de trabalho intensivas, uma base de dados Ambari com especificações mais baixas pode tornar-se um estrangulamento para operações de gestão.
- Flexibilidade - pode escalar a base de dados conforme necessário para atender às suas necessidades.
- Controle - pode gerir backups e segurança para a sua base de dados de uma forma que se enquadre nos requisitos das suas organizações.

O restante deste artigo discute os seguintes pontos:

- requisitos para usar a funcionalidade Ambari DB personalizada
- os passos necessários para fornecer clusters HDInsight usando a sua própria base de dados externa para Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Requisitos personalizados de Ambari DB

Pode implementar um Ambari DB personalizado com todos os tipos e versões de cluster. Vários aglomerados não podem usar o mesmo Ambari DB.

O Ambari DB personalizado tem os seguintes outros requisitos:

- Deve ter um servidor e base de dados Existentes Azure SQL DB.
- A base de dados que fornece para a configuração de Ambari deve estar vazia. Não deve haver mesas no esquema dbo padrão.
- O utilizador utilizado para ligar à base de dados deve ter select, CREATE TABLE e INSERIR permissões na base de dados.
- Ligue a opção de [permitir o acesso aos serviços Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) no servidor Azure SQL onde irá acolher ambari.
- Os endereços IP de gestão do serviço HDInsight precisam de ser permitidos no Servidor SQL. Consulte os endereços IP de [gestão HDInsight](hdinsight-management-ip-addresses.md) para obter uma lista dos endereços IP que devem ser adicionados à firewall do servidor SQL.

Quando hospedar o seu Apache Ambari DB numa base de dados externa, lembre-se dos seguintes pontos:

- Você é responsável pelos custos adicionais do Azure SQL DB que detém Ambari.
- Volte periodicamente ao seu Ambari DB personalizado. A Base de Dados Azure SQL gera cópias de segurança automaticamente, mas o prazo de retenção de cópias de segurança varia. Para mais informações, consulte [Saiba mais sobre cópias automáticas](../sql-database/sql-database-automated-backups.md)de backups da Base de Dados SQL .

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Implementar clusters com um Ambari DB personalizado

Para criar um cluster HDInsight que utilize a sua própria base de dados ambari externa, utilize o [modelo personalizado Ambari DB Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Editar os parâmetros `azuredeploy.parameters.json` no para especificar informações sobre o seu novo cluster e a base de dados que irá manter Ambari.

Pode iniciar a implantação utilizando o Azure CLI. Substitua-o `<RESOURCEGROUPNAME>` pelo grupo de recursos onde pretende implantar o seu cluster.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Passos seguintes

- [Use external metadata stores in Azure HDInsight](hdinsight-use-external-metadata-stores.md) (Utilizar arquivos de metadados externos no Azure HDInsight)