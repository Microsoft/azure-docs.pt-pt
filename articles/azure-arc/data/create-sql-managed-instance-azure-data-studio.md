---
title: Criar exemplo gerido pelo Azure SQL usando o Azure Data Studio
description: Criar exemplo gerido pelo Azure SQL usando o Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280468"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Criar exemplo gerido pelo SQL - Azure Arc usando a Azure Data Studio

Este documento percorre os passos para instalar o Azure SQL caso gerido - Azure Arc usando Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Faça login no controlador de dados do Azure Arc

Antes de criar um caso, inicie sessão no controlador de dados Azure Arc se ainda não tiver iniciado sessão.

```console
azdata login
```

Em seguida, será solicitado o espaço de nome onde o controlador de dados é criado, o nome de utilizador e a palavra-passe para iniciar sessão no controlador.  

> Se precisar validar o espaço de nomes, pode correr ```kubectl get pods -A``` para obter uma lista de todos os espaços de nomes do cluster.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Criar exemplo gerido pelo Azure SQL em Azure Arc

- Lançar Azure Data Studio
- No separador Ligações, clique nos três pontos no topo esquerdo e escolha "Nova Implementação"
- A partir das opções de implementação, selecione **Azure SQL caso gerido - Azure Arc** 
  > [!NOTE]
  > Pode ser solicitado que instale o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] aqui se não estiver instalado.
- Aceite os termos de Privacidade e licença e clique em **Selecionar** na parte inferior



- No exemplo gerido do Deploy Azure SQL - Lâmina Azure Arc, introduza as seguintes informações:
  - Introduza um nome para a instância do Servidor SQL
  - Introduza e confirme uma palavra-passe para a instância do SQL Server
  - Selecione a classe de armazenamento conforme apropriado para os dados
  - Selecione a classe de armazenamento conforme apropriado para os registos

- Clique no **botão Implementar**

- Isto deve iniciar a criação do exemplo gerido Azure SQL - Azure Arc no controlador de dados.

- Em poucos minutos, a sua criação deve completar com sucesso

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Ligue-se ao exemplo gerido do Azure SQL - Azure Arc do Azure Data Studio

- Faça login no controlador de dados Azure Arc, fornecendo o espaço de nome, nome de utilizador e senha para o controlador de dados: 
```console
azdata login
```

- Ver todas as instâncias geridas Azure SQL a provisionadas, utilizando os seguintes comandos:

```console
azdata arc sql mi list
```

A saída deve ser assim, copiar o ServidorEndpoint (incluindo o número de porta) a partir daqui.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- No Azure Data Studio, no separador **Connections,** clique na **nova ligação** na vista **dos Servidores**
- Na lâmina **De Ligação,** cole o ServidorEndpoint na caixa de texto do Servidor
- Selecione **O SESSÃO SQL** como o tipo de autenticação
- *Insira sa* como o nome de utilizador
- Introduza a senha da `sa` conta
- Opcionalmente, insira o nome específico da base de dados para ligar a
- Opcionalmente, selecione/adicione novo grupo de servidores conforme apropriado
- Selecione **Connect** para ligar ao exemplo gerido Azure SQL - Azure Arc




## <a name="next-steps"></a>Passos Seguintes

Agora tente [monitorizar a sua instância SQL](monitor-grafana-kibana.md)
