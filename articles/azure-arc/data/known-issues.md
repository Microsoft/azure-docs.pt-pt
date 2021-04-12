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
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029510"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Questões conhecidas - Azure Arc habilitado serviços de dados (Pré-visualização)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>março de 2021

### <a name="data-controller"></a>Controlador de dados

- Pode criar um controlador de dados em modo de ligação direta com o portal Azure. A implantação com outras ferramentas de serviços de dados ativadas pelo Azure Arc não é suportada. Especificamente, não é possível implantar um controlador de dados no modo de ligação direta com nenhuma das seguintes ferramentas durante esta versão.
   - Azure Data Studio
   - Azure Data CLI `azdata` ()
   - Ferramentas nativas kubernetes

   [Implementar | do controlador de dados Azure Arc O modo de ligação direta](deploy-data-controller-direct-mode.md) explica como criar o controlador de dados no portal. 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc habilitado a hiperescala pósgresql

- Não é suportado para implementar um grupo de servidor de hiperescala pós-escala ativado por Azure Arc num controlador de dados Arc ativado para o modo de ligação direta.
- Passar um valor inválido ao `--extensions` parâmetro ao editar a configuração de um grupo de servidor para permitir extensões adicionais reinicia incorretamente a lista de extensões ativadas para o que era no momento de criação do grupo de servidor e impede o utilizador de criar extensões adicionais. A única solução disponível quando isso acontece é eliminar o grupo de servidor e reimplantá-lo.

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="data-controller"></a>Controlador de dados

- O modo de agrupamento de ligação direta está desativado

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc habilitado a hiperescala pósgresql

- O tempo de restauração do ponto não é suportado por enquanto no armazenamento NFS.
- Não é possível ativar e configurar simultanemente a extensão pg_cron. Tens de usar dois comandos para isto. Um comando para o permitir e um comando para configurá-lo. 

   Por exemplo:
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   O primeiro comando requer um reinício do grupo de servidor. Assim, antes de executar o segundo comando, certifique-se de que o estado do grupo de servidor passou de atualização para pronto. Se executar o segundo comando antes de o reinício ter terminado, falhará. Se for esse o caso, basta esperar por mais alguns momentos e executar o segundo comando novamente.

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


## <a name="next-steps"></a>Passos seguintes

> **Só quer experimentar as coisas?**  
> Inicie-se rapidamente com [o Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) em AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM.

- [Instale as ferramentas do cliente](install-client-tools.md)
- [Criar o controlador de dados Azure Arc](create-data-controller.md) (requer a instalação das ferramentas do cliente primeiro)
- [Criar um exemplo gerido pelo Azure SQL em Azure Arc](create-sql-managed-instance.md) (requer a criação de um controlador de dados Azure Arc primeiro)
- [Criar uma base de dados Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc](create-postgresql-hyperscale-server-group.md) (requer a criação de um controlador de dados Azure Arc primeiro)
- [Resource providers for Azure services](../../azure-resource-manager/management/azure-services-resource-providers.md) (Fornecedor de recursos para serviços do Azure)
- [Notas de lançamento - Azure Arc habilitado serviços de dados (Pré-visualização)](release-notes.md)
