---
title: Azure Arc habilitado serviços de dados - Notas de lançamento
description: Últimas notas de lançamento
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d22976254cc804ca53060fb284abde8e80a684e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319735"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Notas de lançamento - Azure Arc habilitado serviços de dados (Pré-visualização)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Setembro de 2020

Os serviços de dados habilitados Azure Arc são lançados para pré-visualização pública. Os serviços de dados habilitados pelo Arco permitem gerir os serviços de dados em qualquer lugar.

- Instância Gerida do SQL
- Hiperescala pós-alta

Para obter [instruções, o que são os serviços de dados habilitados a Azure Arc?](overview.md)

### <a name="known-issues"></a>Problemas conhecidos

Aplicam-se a esta versão as seguintes questões:

* **Eliminação do grupo de servidores pós-escala PostgreSQL**: Se tiver alterado a configuração do seu grupo ou exemplo de servidor, aguarde que a operação de edição esteja concluída antes de eliminar um grupo de servidores de hiperescala PostgreSQL.

* ** `azdata notebook run` pode falhar:** Para contornar este problema, corra `azdata notebook run` num ambiente virtual Python. Este problema também se manifesta numa tentativa falhada de criar um exemplo gerido pelo SQL ou um grupo de servidores de hiperescala PostgreSQL utilizando o assistente de implementação do Azure Data Studio. Neste caso, pode abrir o caderno e clicar no botão **Executar todos** os botões na parte superior do caderno.

## <a name="next-steps"></a>Passos seguintes

> **Só quer experimentar as coisas?**  
> Inicie-se rapidamente com [o Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) no Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM.

[Instale as ferramentas do cliente](install-client-tools.md)

[Criar o controlador de dados Azure Arc](create-data-controller.md) (requer a instalação das ferramentas do cliente primeiro)

[Criar um exemplo gerido pelo Azure SQL em Azure Arc](create-sql-managed-instance.md) (requer a criação de um controlador de dados Azure Arc primeiro)

[Criar uma base de dados Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc](create-postgresql-hyperscale-server-group.md) (requer a criação de um controlador de dados Azure Arc primeiro)

## <a name="known-limitations-and-issues"></a>Limitações e problemas conhecidos

- Os nomes de exemplos geridos pela SQL não podem ser superiores a 13 caracteres
- Não há atualização no local para o controlador de dados do Arco Azure ou casos de base de dados.
- As imagens do contentor de serviços de dados preparados para o Arc não são assinadas.  Pode ter de configurar os nós do Kubernetes para permitir a solicitação de imagens de contentor não assinadas.  Por exemplo, se estiver a utilizar o Docker como tempo de funcionaamento do contentor, pode definir a variável ambiente DOCKER_CONTENT_TRUST=0 e reiniciar.  Outros runtimes de contentor têm opções semelhantes, como em [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Não é possível criar instâncias geridas pelo SQL ou grupos de servidores de hiperescala PostgreSQL a partir do portal Azure.
- Por enquanto, se estiver a utilizar o NFS, tem de definir o favorecmento do RunAsRoot no seu ficheiro de perfil de implementação antes de criar o controlador de dados Azure Arc.
- Apenas a autenticação de login SQL e PostgreSQL.  Sem suporte para Azure Ative Directory ou Ative Directory.
- A criação de um controlador de dados no OpenShift requer restrições de segurança descontraídas.  Para obter mais detalhes, veja a documentação.
- A redução do número de _nós_ de trabalhadores da Postgres Hyperscale não é suportada.
- Se estiver a utilizar o Motor de Serviço Azure Kubernetes (Motor AKS) no Azure Stack Hub com o controlador de dados Azure Arc e as instâncias de base de dados, o upgrade para uma versão mais recente de Kubernetes não é suportado. Desinstale o controlador de dados do Azure Arc e todas as instâncias da base de dados antes de atualizar o cluster Kubernetes.
- A pré-visualização não suporta backup/restauro para o motor da versão 11 do Postgres. Só suporta backup/restauro para a versão 12 do Postgres.
