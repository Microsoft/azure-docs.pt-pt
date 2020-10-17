---
title: Use o Azure Data Studio para gerir a sua instância PostgreSQL
description: Use o Azure Data Studio para gerir a sua instância PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a073df78adbf0104d81a96eb92ea427f60e53b01
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151845"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Utilize o Azure Data Studio para gerir o seu grupo de servidores de hiperescala Pós-Escala PósgreSQL


Este artigo descreve como:
- gerir os seus casos PostgreSQL com vistas para o painel de instrumentos como Visão Geral, Cordas de Conexão, Propriedades, Saúde de Recursos...
- trabalhar com os seus dados e esquema

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Instalar azdata, Azure Data Studio e Azure CLI](install-client-tools.md)
- Instale no Azure Data Studio as extensões **Azure Data CLI** e **Azure Arc** e **PostgreSQL**
- Criar o [Controlador de Dados do Arco Azul](create-data-controller-using-azdata.md)
- Lançar Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>Ligue-se ao Controlador de Dados do Arco Azure

No Azure Data Studio, expanda os **controladores de arco azure** e selecione o botão **'Controlador de Ligação':**

Introduza as informações de ligação ao seu Controlador de Dados Azure:

- **URL do controlador:**

    O URL para ligar ao seu controlador em Kubernetes. Inserida na forma `https://<IP_address_of_the_controller>:<Kubernetes_port.` de Por exemplo:

    ```console
    https://12.345.67.890:30080
    ```
- **Nome de utilizador:**

    Nome da conta de utilizador que utiliza para ligar ao Controlador. Utilize o nome que normalmente utiliza quando `azdata login` corre. Não é o nome do utilizador PostgreSQL que usa para ligar ao motor de base de dados PostgreSQL tipicamente a partir de psql.
- **Senha:** A palavra-passe da conta de utilizador que utiliza para ligar ao Controlador


O estúdio de dados Azure mostra o seu Controlador de Dados do Arco. Expanda-o e mostra a lista de casos postgreSQL que gere.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Gerencie os seus grupos de servidores de hiperescala pós-escala Azure Arc

Clique com o botão direito na instância PostgreSQL que pretende gerir e selecionar [Gerir]

A vista do painel postgreSQL:

Que apresenta vários dashboards listados no lado esquerdo do painel:

- **Visão geral:** Exibe informações sumárias sobre o seu caso como nome, nome de utilizador pós-SQL, ID de assinatura Azure, configuração, versão do motor de base de dados, pontos finais para Grafana e Kibana...
- **Cadeias de ligação:** Exibe várias cordas de ligação que poderá necessitar de se ligar ao seu exemplo postgreSQL como psql, Node.js, PHP, Ruby...
- **Diagnosticar e resolver problemas:** É a página de aterragem onde você vai encontrar vários recursos que o ajudarão a resolver o seu caso à medida que expandimos os cadernos de resolução de problemas
- **Novo pedido de apoio:** É a página de aterragem a partir da qual poderá solicitar assistência dos nossos serviços de apoio a partir do anúncio de Pré-Visualização Pública.

## <a name="work-with-your-data-and-schema"></a>Trabalhe com os seus dados e esquema

No lado esquerdo da janela do Azure Data Studio, expanda os **servidores**de nó:

E selecione [Adicionar Ligação] e preencha os detalhes da ligação à sua instância PostgreSQL:
- **Tipo de ligação:** Postgresql
- **Nome do servidor:** insira o nome da sua instância PostgreSQL. Por exemplo: postgres01
- **Tipo de autenticação:** Senha
- **Nome do utilizador:** por exemplo, pode utilizar o nome de utilizador pós-administração PostgreSQL padrão/predefinido. Nota, este campo é sensível a casos.
- **Palavra-passe:** encontrará a palavra-passe do nome de utilizador PostgreSQL na cadeia de ligação psql na saída do `azdata postgres server endpoint -n postgres01` comando
- **Nome da base de dados:** desa um nome da base de dados a que pretende ligar. Pode deixá-lo definido como __Predefinido__
- **Grupo de servidor:** pode deixá-lo definir para __Predefinição__
- **Nome (opcional):** pode deixar este em branco
- **Avançado:**
    - **Endereço IP anfitrião:** é o endereço IP público do cluster Kubernetes
    - **Porta:** é a porta onde o seu caso PostgreSQL está a ouvir. Pode encontrar esta porta no final da cadeia de ligação psql na saída do `azdata postgres server endpoint -n postgres01` comando. Não porta 30080 em que kubernetes está a ouvir e que inseriu ao ligar-se ao Controlador de Dados Azure no Azure Data Studio.
    - **Outros parâmetros:** Devem ser auto-explícitos, pode viver com os valores predefinidos/em branco com os que aparecem.

Selecione **[OK] e [Conecte-se]** para ligar ao seu servidor.

Uma vez conectados, várias experiências estão disponíveis:
- **Nova consulta**
- **Novo Caderno**
- **Expanda a exibição do seu servidor e navegue/trabalhe nos objetos dentro da sua base de dados**
- **...**

## <a name="next-step"></a>Passo seguinte
[Monitorize o seu grupo de servidor](monitor-grafana-kibana.md)
