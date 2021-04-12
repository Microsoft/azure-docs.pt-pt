---
title: 'Tutorial: Dados de acesso em Cosmos DB usando Mongoose com Azure Static Web Apps'
description: Aprenda a aceder a dados em Cosmos DB usando o Mongoose a partir de uma função API de Aplicações Web Estáticas Azure.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260071"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>Tutorial: Dados de acesso em Cosmos DB usando Mongoose com Azure Static Web Apps

[Mongoose](https://mongoosejs.com/) é o cliente ODM (Object Document Mapping) mais popular para Node.js. Permitindo-lhe conceber uma estrutura de dados e impor validação, a Mongoose fornece todas as ferramentas necessárias para interagir com bases de dados que suportam a API mongoose. [Cosmos DB](../cosmos-db/mongodb-introduction.md) suporta as APIs mongoose necessárias e está disponível como uma opção de servidor back-end em Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar uma conta cosmos dB sem servidor
> - Criar aplicativos web estáticos Azure
> - Atualizar as definições da aplicação para armazenar a cadeia de ligação

Se não tiver uma subscrição do Azure, crie uma [conta de teste gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Uma [conta do Azure](https://azure.microsoft.com/free/)
- Uma [conta do GitHub](https://github.com/join)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-cosmos-db-serverless-database"></a>Criar uma base de dados sem servidores Cosmos DB

Comece por criar uma conta [sem servidor Cosmos DB.](https://docs.microsoft.com/azure/cosmos-db/serverless) Ao utilizar uma conta sem servidor, você só paga pelos recursos que são usados e evita a necessidade de criar uma infraestrutura completa.

1. Navegar para [https://portal.azure.com](https://portal.azure.com)
2. Clique **em Criar um recurso**
3. **Insira Azure Cosmos DB** na caixa de pesquisa
4. Clique em **Azure Cosmos DB**
5. Clique em **Criar**
6. Configure a sua Conta DB Azure Cosmos com as seguintes informações
    - Subscrição: Escolha a subscrição que deseja utilizar
    - Recurso: Clique em **Criar novo**, e definir o nome para **aswa-mongoose**
    - Nome da conta: É necessário um valor único
    - API: **Azure Cosmos DB para MongoDB API**
    - Cadernos (Pré-visualização): **Desligado**
    - Localização: **West US 2**
    - Modo de capacidade: **Serverless (pré-visualização)**
    - Versão: **3.6**
    - Zonas de disponibilidade: **Desativar** 
 :::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="Criar nova instância de DB do Cosmos":::
7. Clique **em Rever + criar**
8. Clique em **Criar**

O processo de criação levará alguns minutos. Os passos posteriores voltarão à base de dados para recolher a cadeia de ligação.

## <a name="create-a-static-web-app"></a>Criar uma aplicação Web estática

Este tutorial usa um repositório de modelos GitHub para ajudá-lo a criar a sua aplicação.

1. Navegue para o [modelo de arranque](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)
2. Escolha o **proprietário** (se utilizar uma organização diferente da sua conta principal)
3. Nomeie o seu repositório **aswa-mongoose-tutorial**
4. Clique **em Criar repositório a partir do modelo**
5. Regresso ao [portal Azure](https://portal.azure.com)
6. Clique **em Criar um recurso**
7. Digite **aplicativos web estáticos** na caixa de pesquisa
8. Selecione **Estática Web App (pré-visualização)**
9. Clique em **Criar**
10. Configure a sua App Web Estática Azure com as seguintes informações
    - Subscrição: Escolha a mesma subscrição de antes
    - Grupo de recursos: Selecione **aswa-mongoose**
    - Nome: **aswa-mangoose-tutorial**
    - Região: **Oeste DOS EUA 2**
    - Clique **em iniciar s-sção com GitHub**
    - Clique **em Autorizar** se solicitado para permitir que a Azure Static Web Apps crie a Ação GitHub para permitir a implementação
    - Organização: O nome da sua conta
    - Repositório: **aswa-mongoose-tutorial**
    - Ramo: **principal**
    - Construir predefinições: Escolha **o costume**
    - Localização da aplicação: **/público**
    - Localização Api: **api**
    - Localização do artefacto da app: *deixe em branco* Formulário de 
     :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="Aplicações Web Estáticas Azure":::
11. Clique **em Rever e criar**
12. Clique em **Criar**

O processo de criação demora alguns minutos, mas pode clicar em **Ir ao recurso** assim que a aplicação for aprovisionada.

## <a name="configure-database-connection-string"></a>Cadeia de ligação de base de dados de configuração

Para permitir que a aplicação web comunique com a base de dados, o fio de ligação da base de dados é armazenado como uma Definição de Aplicação. Os valores de regulação são acessíveis Node.js utilizando o `process.env` objeto.

1. Clique em **casa** no canto superior esquerdo do portal Azure (ou volte [https://portal.azure.com](https://portal.azure.com) para)
2. Clique **em grupos de recursos**
3. Clique **em aswa-mongoose**
4. Clique no nome da sua conta de base de dados - terá um tipo de **Conta DB Azure Cosmos**
5. Em **Definições** clique em **Cadeia de Ligação**
6. Copie a cadeia de ligação listada **na STRING DE LIGAÇÃO PRIMÁRIA**
7. Nas migalhas de pão, clique **em aswa-mongoose**
8. Clique **em aswa-mongoose-tutorial** para voltar à instância do site
9. Em **Definições** clique **em Configuração**
10. Clique **em Adicionar** e crie uma nova Definição de Aplicação com os seguintes valores
    - Nome: **CONNECTION_STRING**
    - Valor: Cole a cadeia de ligação que copiou anteriormente
11. Clique **em OK**
12. Clique em **Guardar**

## <a name="navigate-to-your-site"></a>Navegue para o seu site

Agora pode explorar a aplicação web estática.

1. Clique **na visão geral**
1. Clique no URL exibido no canto superior direito
    1. Vai parecer-se com `https://calm-pond-05fcdb.azurestaticapps.net`
1. Crie uma nova tarefa digitando um título e clicando na **tarefa Adicionar**
1. Confirme que a tarefa é exibida (pode demorar um momento)
1. Marque a tarefa como completa **clicando na caixa de verificação**
1. **Atualize a página** para confirmar que uma base de dados está a ser usada

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o grupo de recursos com os seguintes passos:

1. Regresso ao [portal Azure](https://portal.azure.com)
2. Clique **em grupos de recursos**
3. Clique **em aswa-mongoose**
4. Clique **em Eliminar grupo de recursos**
5. Digite **aswa-mangoose** na caixa de texto
6. Clique em **Delete** (Eliminar)

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a configurar o desenvolvimento local...
> [!div class="nextstepaction"]
> [Configurar desenvolvimento local](./local-development.md)
 
