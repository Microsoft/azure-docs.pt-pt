---
title: Azure Arc permitiu serviços de dados - questões conhecidas
description: Últimos problemas conhecidos
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 8100d9e12f107e0c4598876c46453b46c6ee4d0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122005"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Questões conhecidas - Azure Arc habilitado serviços de dados (Pré-visualização)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Fevereiro de 2021

- O modo de cluster ligado é desativado

## <a name="introduced-prior-to-february-2021"></a>Introduzido antes de fevereiro de 2021

### <a name="data-controller"></a>Controlador de dados

- No Serviço Azure Kubernetes (AKS), a versão 1.19.x da Kubernetes não é suportada.
- Em Kubernetes 1.19 `containerd` não é suportado.
- O recurso de controlador de dados em Azure é atualmente um recurso Azure. Quaisquer atualizações como a eliminação não são propagadas de volta ao cluster kubernetes.
- Os nomes de exemplos não podem ser superiores a 13 caracteres
- Não há atualização no local para o controlador de dados do Arco Azure ou casos de base de dados.
- As imagens do contentor de serviços de dados preparados para o Arc não são assinadas.  Pode ter de configurar os nós do Kubernetes para permitir a solicitação de imagens de contentor não assinadas.  Por exemplo, se estiver a utilizar o Docker como tempo de funcionaamento do contentor, pode definir a variável ambiente DOCKER_CONTENT_TRUST=0 e reiniciar.  Outros runtimes de contentor têm opções semelhantes, como em [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Não é possível criar instâncias geridas pelo SQL ou grupos de servidores de hiperescala PostgreSQL a partir do portal Azure.
- Por enquanto, se estiver a utilizar o NFS, tem de definir `allowRunAsRoot` no seu ficheiro de perfil de `true` implementação antes de criar o controlador de dados Azure Arc.
- Apenas a autenticação de login SQL e PostgreSQL.  Sem suporte para Azure Ative Directory ou Ative Directory.
- A criação de um controlador de dados no OpenShift requer restrições de segurança descontraídas.  Para obter mais detalhes, veja a documentação.
- Se estiver a utilizar o motor do Serviço Azure Kubernetes (AKS) no Azure Stack Hub com o controlador de dados Azure Arc e as instâncias de base de dados, o upgrade para uma versão mais recente de Kubernetes não é suportado. Desinstale o controlador de dados do Azure Arc e todas as instâncias da base de dados antes de atualizar o cluster Kubernetes.
- Os clusters AKS que abrangem [várias zonas de disponibilidade](../../aks/availability-zones.md) não são atualmente suportados para serviços de dados habilitados a Azure Arc. Para evitar este problema, quando criar o cluster AKS no portal Azure, se selecionar uma região onde as zonas estão disponíveis, limpe todas as zonas do controlo de seleção. Veja a imagem seguinte:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Limpe as caixas de verificação para cada zona especificar nenhuma.":::

### <a name="postgresql"></a>PostgreSQL

- Azure Arc ativado PostgreSQL Hyperscale devolve uma mensagem de erro imprecisa quando não consegue restaurar o ponto relativo no tempo indicado. Por exemplo, se especificou um ponto no tempo para restaurar o que é mais antigo do que o que as suas cópias de segurança contêm, a restauração falhará com uma mensagem de erro como: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
Quando isto acontecer, reinicie o comando depois de indicar um ponto no tempo que está dentro do intervalo de datas para as quais tem cópias de segurança. Irá determinar esta gama listando as suas cópias de segurança e analisando as datas em que foram tomadas.
- A restauração pontual é suportada apenas em grupos de servidores. O servidor alvo de um ponto de restauração no tempo não pode ser o servidor a partir do qual obteve a cópia de segurança. Tem de ser um grupo de servidores diferente. No entanto, a restauração total é suportada no mesmo grupo de servidores.
- É necessário um id de reserva quando se faz um restauro completo. Por predefinição, se não estiver a indicar um id de backup, a cópia de segurança mais recente será utilizada. Isto não funciona nesta versão.

## <a name="next-steps"></a>Passos seguintes

> **Só quer experimentar as coisas?**  
> Inicie-se rapidamente com [o Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) em AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM.

- [Instale as ferramentas do cliente](install-client-tools.md)
- [Criar o controlador de dados Azure Arc](create-data-controller.md) (requer a instalação das ferramentas do cliente primeiro)
- [Criar um exemplo gerido pelo Azure SQL em Azure Arc](create-sql-managed-instance.md) (requer a criação de um controlador de dados Azure Arc primeiro)
- [Criar uma base de dados Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc](create-postgresql-hyperscale-server-group.md) (requer a criação de um controlador de dados Azure Arc primeiro)
- [Resource providers for Azure services](../../azure-resource-manager/management/azure-services-resource-providers.md) (Fornecedor de recursos para serviços do Azure)
- [Notas de lançamento - Azure Arc habilitado serviços de dados (Pré-visualização)](release-notes.md)
