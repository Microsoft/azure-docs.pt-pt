---
title: Base de dados personalizada Apache Ambari em Azure HDInsight
description: Saiba como criar clusters HDInsight com a sua própria base de dados Apache Ambari personalizada.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: fe38ddc594060c78a2d26e9b25476e38736b4cf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946055"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Configurar clusters HDInsight com um DB Ambari personalizado

Apache Ambari simplifica a gestão e monitorização de um cluster Apache Hadoop. Ambari fornece uma UI web fácil de usar e REST API. Ambari está incluído em clusters HDInsight, e é usado para monitorizar o cluster e fazer alterações de configuração.

Na criação normal de clusters, como descrito em outros artigos como [Configurar clusters em HDInsight,](hdinsight-hadoop-provision-linux-clusters.md)Ambari é implantado numa [Base de Dados S0 Azure SQL](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) que é gerida por HDInsight e não é acessível aos utilizadores.

A funcionalidade DB personalizada permite-lhe implementar um novo cluster e configurar o Ambari numa base de dados externa que gere. A implementação é feita com um modelo de Gestor de Recursos Azure. Esta funcionalidade tem os seguintes benefícios:

- Personalização - escolha o tamanho e capacidade de processamento da base de dados. Se tiver grandes clusters a processar cargas de trabalho intensivas, uma base de dados Ambari com especificações mais baixas pode tornar-se um estrangulamento para as operações de gestão.
- Flexibilidade - pode escalar a base de dados conforme necessário para atender às suas necessidades.
- Controle - pode gerir backups e segurança para a sua base de dados de uma forma que se enquadre nos requisitos das suas organizações.

O restante deste artigo discute os seguintes pontos:

- requisitos para usar a funcionalidade DB Ambari personalizada
- os passos necessários para a disponibilização de clusters HDInsight utilizando a sua própria base de dados externa para Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Requisitos personalizados de DB Ambari

Você pode implementar um DB Ambari personalizado com todos os tipos e versões de cluster. Vários clusters não podem usar o mesmo Ambari DB.

O Ambari DB personalizado tem os seguintes outros requisitos:

- O nome da base de dados não pode conter hífens ou espaços
- Deve ter um servidor Escriba DB Azure existente e base de dados.
- A base de dados que fornece para a instalação do Ambari deve estar vazia. Não deve haver tabelas no esquema dbo padrão.
- O utilizador utilizado para ligar à base de dados deve ter permissões SELECT, CREATE TABLE e INSERT na base de dados.
- Ligue a opção para Permitir o [acesso aos serviços Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) no servidor onde irá acolher a Ambari.
- Os endereços IP de gestão do serviço HDInsight devem ser permitidos na regra da firewall. Consulte [endereços IP de gestão hdInsight](hdinsight-management-ip-addresses.md) para uma lista dos endereços IP que devem ser adicionados à regra de firewall ao nível do servidor.

Quando hospedar o seu Apache Ambari DB numa base de dados externa, lembre-se dos seguintes pontos:

- Você é responsável pelos custos adicionais do Azure SQL DB que detém Ambari.
- Apoie periodicamente o seu DB Ambari personalizado. A base de dados Azure SQL gera cópias de segurança automaticamente, mas o prazo de retenção de backup varia. Para obter mais informações, consulte [as cópias de segurança automáticas da Base de Dados SQL.](../azure-sql/database/automated-backups-overview.md)

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Implementar clusters com um DB Ambari personalizado

Para criar um cluster HDInsight que utiliza a sua própria base de dados Ambari externa, utilize o [modelo de arranque rápido Ambari DB personalizado.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)

Edite os parâmetros no `azuredeploy.parameters.json` para especificar informações sobre o seu novo cluster e a base de dados que irá deter Ambari.

Pode iniciar a implantação utilizando o Azure CLI. `<RESOURCEGROUPNAME>`Substitua-o pelo grupo de recursos onde pretende implantar o seu cluster.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="database-sizing"></a>Dimensionamento de base de dados

A tabela a seguir fornece diretrizes sobre as quais o nível DB Azure SQL para selecionar com base no tamanho do seu cluster HDInsight.

| Número de nós de trabalhadores | Nível DB necessário |
|---|---|
| <=4 | S0 |
| >4 && <=8 | S1 |
| >8 && <=16 | S2 |
| >16 && <=32 | S3 |
| >32 && <=64 | S4 |
| >64 && <=128 | P2 |
| >128 | Contactar o Suporte |

## <a name="next-steps"></a>Passos seguintes

- [Use external metadata stores in Azure HDInsight](hdinsight-use-external-metadata-stores.md) (Utilizar arquivos de metadados externos no Azure HDInsight)
