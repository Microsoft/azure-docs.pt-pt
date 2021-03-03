---
title: Migrar o MongoDB offline para Azure Cosmos DB API para a MongoDB, utilizando ferramentas nativas mongoDB
description: Saiba como as ferramentas nativas do MongoDB podem ser usadas para migrar pequenos conjuntos de dados de instâncias MongoDB para Azure Cosmos DB
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2e9f3c877a5c4650d3e31fa414cac76837f4c9e8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655756"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Tutorial: Migrar a MongoDB para a API da Azure Cosmos para a MongoDB offline usando ferramentas nativas do MongoDB

Você pode usar ferramentas nativas mongoDB para realizar uma migração offline (uma vez) de bases de dados de um local ou exemplo de nuvem de MongoDB para AZure Cosmos DB API para MongoDB.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Escolha a ferramenta nativa mongoDB apropriada para a sua caixa de uso
> * Executar a migração.
> * Monitorizar a migração.
> * Verifique se a migração foi bem sucedida.

Neste tutorial, você migra um conjunto de dados em MongoDB hospedado em uma máquina virtual Azure para Azure Cosmos DB's API para MongoDB usando ferramentas nativas mongoDB. As ferramentas nativas do MongoDB são um conjunto de binários que facilitam a manipulação de dados num caso mongoDB existente. Uma vez que a Azure Cosmos DB expõe uma API mongo, as ferramentas nativas mongoDB são capazes de inserir dados no Azure Cosmos DB. O foco deste doc está em dados migratórios de um caso MongoDB usando *o mongoexport/mongoimport* ou *mongodump/mongorestore*. Uma vez que as ferramentas nativas se ligam ao MongoDB usando cordas de ligação, pode executar as ferramentas em qualquer lugar, no entanto recomendamos que execute estas ferramentas dentro da mesma rede que a instância MongoDB para evitar problemas de firewall. 

As ferramentas nativas do MongoDB só podem mover dados tão rapidamente quanto o hardware do anfitrião permite; as ferramentas nativas podem ser a solução mais simples para pequenos conjuntos de dados onde o tempo total de migração não é uma preocupação. [O conector De Faísca MongoDB](https://docs.mongodb.com/spark-connector/current/), [O Serviço de Migração de Dados (DMS)](../dms/tutorial-mongodb-cosmos-db.md)ou [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) podem ser melhores alternativas se precisar de um gasoduto de migração escalável.

Se ainda não tiver uma fonte MongoDB configurada, consulte o artigo [Instalar e configurar o MongoDB num Windows VM em Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Complete os passos [de pré-migração,](../cosmos-db/mongodb-pre-migration.md) tais como a estimativa da produção, a escolha de uma chave de partição e a política de indexação.
* [Criar uma API DB AZure Cosmos para a conta MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Inicie sessão no seu caso MongoDB
    * [Descarregue e instale as ferramentas nativas mongoDB a partir deste link](https://www.mongodb.com/try/download/database-tools).
        * **Certifique-se de que a sua versão de ferramentas nativas MongoDB corresponde à sua instância MongoDB existente.**
        * Se a sua instância MongoDB tiver uma versão diferente da Azure Cosmos DB Mongo API, **então instale ambas as versões de ferramentas nativas do MongoDB e utilize a versão adequada da ferramenta para a MongoDB e Azure Cosmos DB Mongo API, respectivamente.**
    * Adicione um utilizador com `readWrite` permissões, a menos que já exista. Mais tarde neste tutorial, forneça este nome de utilizador/palavra-passe às ferramentas *mongoexport* e *mongodump.*

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Configurar Azure Cosmos DB Server Side Retries

Os clientes que migram de MongoDB para Azure Cosmos DB beneficiam de capacidades de governação de recursos, que garantem a capacidade de utilizar plenamente os seus RU/s de produção. A Azure Cosmos DB pode acelerar um determinado pedido no decurso da migração se esse pedido exceder o contentor a provisionado RU/s; então esse pedido precisa de ser novamente julgado. O tempo de ida e volta envolvido no salto de rede entre a ferramenta de migração e a Azure Cosmos DB tem impacto no tempo de resposta global desse pedido; além disso, as ferramentas nativas mongoDB não podem lidar com retróctos. A funcionalidade de *Retry side* do servidor da Azure Cosmos DB permite ao serviço intercetar códigos de erro do acelerador e voltar a tentar com um tempo de ida e volta muito mais baixo, melhorando drasticamente os tempos de resposta do pedido. Do ponto de vista das ferramentas nativas do MongoDB, a necessidade de lidar com as retrótonas é minimizada, o que impactará positivamente a sua experiência durante a migração.

Pode encontrar a capacidade de relagem do lado do servidor na lâmina *características* do portal DB Azure Cosmos

![Screenshot da função SSR mongoDB.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

E se for *desativado,* então recomendamos que o ative como mostrado abaixo

![Screenshot da MongoDB SSR ativa.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Escolha a ferramenta nativa mongoDB adequada

![Diagrama de selecionar a melhor ferramenta nativa mongoDB.](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* é o melhor par de ferramentas de migração para migrar um subconjunto da sua base de dados MongoDB.
    * *a mongoexport* exporta os seus dados existentes para um ficheiro JSON ou CSV legível pelo homem. *a mongoexport* tem um argumento que especifica o subconjunto dos seus dados existentes para exportar. 
    * *a mongoimport* abre um ficheiro JSON ou CSV e insere o conteúdo na caixa de dados-alvo (Azure Cosmos DB neste caso.). 
    * Note que JSON e CSV não são formatos compactos; pode incorrer em encargos de rede em excesso, uma vez *que a Mongoimport* envia dados para a Azure Cosmos DB.
* *mongodump/mongorestore* é o melhor par de ferramentas de migração para migrar toda a sua base de dados MongoDB. O formato BSON compacto fará uma utilização mais eficiente dos recursos de rede à medida que os dados são inseridos na Azure Cosmos DB.
    * *a Mongodump* exporta os seus dados existentes como ficheiro BSON.
    * *Mongorestore* importa o seu depósito de arquivo BSON em Azure Cosmos DB.
* Como um aparte - se você simplesmente tem um pequeno arquivo JSON que você quer importar para Azure Cosmos DB Mongo API, a ferramenta *mongoimport* é uma solução rápida para ingerir os dados.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Colete as credenciais da Azure Cosmos DB Mongo API

A Azure Cosmos DB Mongo API fornece credenciais de acesso compatíveis que as ferramentas nativas mongoDB podem utilizar. Você precisará ter estas credenciais de acesso à mão para migrar dados para Azure Cosmos DB Mongo API. Para encontrar estas credenciais:

1. Abra o portal do Azure
1. Navegue para a sua conta Azure Cosmos DB Mongo API
1. No navegador esquerdo, selecione a lâmina *de corda de ligação* e deverá ver um visor semelhante ao seguinte:

    ![Screenshot das credenciais de Azure Cosmos DB.](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *HOST* - O ponto final do Azure Cosmos DB funciona como nome anfitrião do MongoDB
    * *PORT* - quando as ferramentas nativas mongoDB se ligarem ao Azure Cosmos DB, você deve especificar esta porta explicitamente
    * *USERNAME* - o prefixo do nome de domínio do ponto final Azure Cosmos DB funciona como nome de utilizador MongoDB
    * *PASSWORD* - o Azure Cosmos DB funções-chave como a palavra-passe MongoDB
    * Além disso, note o campo *SSL* que é `true` - a ferramenta nativa MongoDB **deve** permitir SSL ao escrever dados em Azure Cosmos DB

## <a name="perform-the-migration"></a>Realizar a migração

1. Escolha qual base de dados e recolha(s) que pretende migrar. Neste exemplo, estamos a migrar a recolha de *consultas* na base de dados *edx* de MongoDB para Azure Cosmos DB.

O resto desta secção irá guiá-lo através do par de ferramentas selecionadas na secção anterior.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Para exportar os dados da fonte da origem, abrimos um terminal na máquina de instância MongoDB. Se for uma máquina Linux, escreva

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    Nas janelas, o executável `mongoexport.exe` será. *HOST*, *PORT,* *USERNAME* e *PASSWORD* devem ser preenchidos com base nas propriedades da sua instância de base de dados MongoDB existente. 
    
    Também pode optar por exportar apenas um subconjunto do conjunto de dados MongoDB. Uma forma de o fazer é adicionando um argumento adicional de filtro:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Serão exportados apenas documentos que correspondam ao `{"field1":"value1"}` filtro.

    Uma vez executada a chamada, deve ver se é produzido um `edx.json` ficheiro:

    ![Screenshot da chamada do mongoexport.](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. Pode usar o mesmo terminal para `edx.json` importar para a Azure Cosmos DB. Se estiver a correr `mongoimport` numa máquina Linux, escreva

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    No Windows, o executável será `mongoimport.exe` . *HOST*, *PORT*, *USERNAME* e *PASSWORD* devem ser preenchidos com base nas credenciais DB do Azure Cosmos que recolheu anteriormente. 
1. **Monitorize** a saída do terminal a partir de *Mongoimport*. Deve ver que imprime linhas de texto ao terminal contendo atualizações sobre o estado da migração:

    ![Screenshot da chamada de Mongoimport.](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Finalmente, examine a Azure Cosmos DB para **validar** que a migração foi bem sucedida. Abra o portal Azure Cosmos DB e navegue até ao Data Explorer. Deve ver (1) que foi criada uma base de dados *edx* com uma coleção *Descomunerada Importada,* e (2) se exportar apenas um subconjunto de dados, *importados OQuery* deve conter *apenas* documentos correspondentes ao subconjunto pretendido dos dados. No exemplo abaixo, apenas um doc correspondeu ao `{"field1":"value1"}` filtro:

    ![Screenshot da verificação de dados do Cosmos DB.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. Para criar um depósito de dados BSON da sua instância MongoDB, abra um terminal na máquina de instância MongoDB. Se for uma máquina Linux, escreva

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    *HOST*, *PORT,* *USERNAME* e *PASSWORD* devem ser preenchidos com base nas propriedades da sua instância de base de dados MongoDB existente. Deve ver que é produzido um `edx-dump` diretório e que a estrutura do diretório reproduz a hierarquia de `edx-dump` recursos (base de dados e estrutura de recolha) da sua origem, a instância MongoDB. Cada coleção é representada por um ficheiro BSON:

    ![Screenshot da chamada de Mongodump.](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. Pode utilizar o mesmo terminal para restaurar o conteúdo da `edx-dump` Azure Cosmos DB. Se estiver a correr `mongorestore` numa máquina Linux, escreva

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    No Windows, o executável será `mongorestore.exe` . *HOST*, *PORT*, *USERNAME* e *PASSWORD* devem ser preenchidos com base nas credenciais DB do Azure Cosmos que recolheu anteriormente. 
1. **Monitorize** a saída do terminal a partir de *mongorestore.* Deve ver se imprime linhas para o terminal de atualização do estado de migração:

    ![Screenshot da chamada de Mongorestore.](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Finalmente, examine a Azure Cosmos DB para **validar** que a migração foi bem sucedida. Abra o portal Azure Cosmos DB e navegue até ao Data Explorer. Deve ver (1) que foi criada uma base de dados *edx* com uma coleção *DescontedQuery,* e (2) *importadoQuery* deve conter *todo* o conjunto de dados da recolha de origem:

    ![Screenshot de verificar os dados de mongorestore da Cosmos DB.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Otimização pós-migração

Depois de migrar os dados armazenados na base de dados mongoDB para a API da Azure Cosmos DB para o MongoDB, pode ligar-se à Azure Cosmos DB e gerir os dados. Também pode executar outros passos de otimização pós-migração, tais como otimizar a política de indexação, atualizar o nível de consistência padrão ou configurar a distribuição global para a sua conta DB Azure Cosmos. Para mais informações, consulte o artigo [de otimização pós-migração.](../cosmos-db/mongodb-post-migration.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Informações de serviço do Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)
* [Documentação de ferramentas de base de dados mongoDB](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Passos seguintes

* Reveja as orientações de migração para cenários adicionais no [Guia de Migração da Base de Dados da](https://datamigration.microsoft.com/)Microsoft .