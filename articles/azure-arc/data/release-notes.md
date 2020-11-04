---
title: Azure Arc habilitado serviços de dados - Notas de lançamento
description: Últimas notas de lançamento
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: 82dd2f16fa43b52ba4c6dfacd26da5da622523b2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321713"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Notas de lançamento - Azure Arc habilitado serviços de dados (Pré-visualização)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Outubro de 2020 

Número da versão Azure Data CLI `azdata` ( 20.2.3. Faça o download em [https://aka.ms/azdata](https://aka.ms/azdata) .

### <a name="breaking-changes"></a>Alterações interruptivas

Esta versão introduz as seguintes alterações de rutura: 

* Na definição de recurso personalizado PostgreSQL (CRD), o termo `shards` é renomeado para `workers` . Este termo corresponde `workers` ao nome do parâmetro da linha de comando.

* `azdata arc postgres server delete` solicita a confirmação antes de apagar uma instância postgres.  Use `--force` para saltar o pedido.

### <a name="additional-changes"></a>Alterações adicionais

* Um novo parâmetro opcional foi adicionado ao `azdata arc postgres server create` chamado `--volume-claim mounts` . O valor é uma vírgula separada lista de suportes de reivindicação de volume. Um suporte de reivindicação de volume é um par de tipo de volume e nome de PVC. O único tipo de volume atualmente suportado é `backup` .  No PostgreSQL, quando o tipo de volume `backup` é, o PVC é montado para `/mnt/db-backups` .  Isto permite a partilha de backups entre instâncias PostgresSQL para que a cópia de segurança de uma instância PostgresSQL possa ser restaurada em outro caso.

* Um novo nome curto para definições de recursos personalizados PostgresSQL: 

  * `pg11` 

  * `pg12`

* O upload de telemetria por fornece ao utilizador:

   * Número de pontos enviados para Azure

     ou 

   * Se nenhum dado tiver sido carregado para Azure, um pedido para tentar novamente.

* `azdata arc dc debug copy-logs` agora também lê a partir de `/var/opt/controller/log` pasta e recolhe registos de motores PostgreSQL no Linux.

*   Apresente um indicador de trabalho durante a criação e restauro da cópia de segurança com a Hiperescala PostgreSQL.

* `azdata arc postrgres backup list` agora inclui informações sobre o tamanho da cópia de segurança.

* A propriedade do nome de administração sql Managed Instance foi adicionada à coluna direita da lâmina de visão geral no portal Azure.

* O Azure Data Studio suporta o número de nós de trabalho, vCore e definições de memória para a Escala de Hiperescala PostgreSQL. 

* A pré-visualização suporta o backup/restauro para as versões 11 e 12 do Postgres.

## <a name="september-2020"></a>Setembro de 2020

Os serviços de dados habilitados Azure Arc são lançados para pré-visualização pública. Os serviços de dados habilitados pelo Arco permitem gerir os serviços de dados em qualquer lugar.

- Instância Gerida do SQL
- Hiperescala pós-alta

Para obter [instruções, o que são os serviços de dados habilitados a Azure Arc?](overview.md)

## <a name="known-limitations-and-issues"></a>Limitações e problemas conhecidos

- Os nomes de exemplo não podem ser superiores a 13 caracteres
- Não há atualização no local para o controlador de dados do Arco Azure ou casos de base de dados.
- As imagens do contentor de serviços de dados preparados para o Arc não são assinadas.  Pode ter de configurar os nós do Kubernetes para permitir a solicitação de imagens de contentor não assinadas.  Por exemplo, se estiver a utilizar o Docker como tempo de funcionaamento do contentor, pode definir a variável ambiente DOCKER_CONTENT_TRUST=0 e reiniciar.  Outros runtimes de contentor têm opções semelhantes, como em [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Não é possível criar instâncias geridas pelo SQL ou grupos de servidores de hiperescala PostgreSQL a partir do portal Azure.
- Por enquanto, se estiver a utilizar o NFS, tem de definir `allowRunAsRoot` no seu ficheiro de perfil de `true` implementação antes de criar o controlador de dados Azure Arc.
- Apenas a autenticação de login SQL e PostgreSQL.  Sem suporte para Azure Ative Directory ou Ative Directory.
- A criação de um controlador de dados no OpenShift requer restrições de segurança descontraídas.  Para obter mais detalhes, veja a documentação.
- Se estiver a utilizar o Motor de Serviço Azure Kubernetes (Motor AKS) no Azure Stack Hub com o controlador de dados Azure Arc e as instâncias de base de dados, o upgrade para uma versão mais recente de Kubernetes não é suportado. Desinstale o controlador de dados do Azure Arc e todas as instâncias da base de dados antes de atualizar o cluster Kubernetes.
- Azure Kubernetes Service (AKS), clusters que abrangem [várias zonas de disponibilidade](../../aks/availability-zones.md) não são atualmente suportados para serviços de dados habilitados a Azure Arc. Para evitar este problema, quando criar o cluster AKS no portal Azure, se selecionar uma região onde as zonas estão disponíveis, limpe todas as zonas do controlo de seleção. Veja a imagem seguinte:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Limpe as caixas de verificação para cada zona especificar nenhuma.":::


### <a name="known-issues-for-azure-arc-enabled-postgresql-hyperscale"></a>Problemas conhecidos para Azure Arc Enabled PostgreSQL Hyperscale   

- A pré-visualização não suporta a cópia de segurança/restauro do motor da versão 11 do PostgreSQL. Só suporta backup/restauro para a versão 12 do PostgreSQL.
- `azdata arc dc debug copy-logs` Ndoes não recolhem registos de motores PostgreSQL no Windows.
- Recriar um grupo de servidor com o nome de um grupo de servidor que foi eliminado pode falhar ou pendurar. 
   - **Solução alternativa** Não reutilizar o mesmo nome quando recriar um grupo de servidor ou esperar pelo balanceador de carga/serviço externo do grupo de servidores previamente eliminado. Assumindo que o nome do grupo de servidor que apagou era `postgres01` e que estava hospedado num espaço de nome , antes de recriar um grupo de servidor com o `arc` mesmo nome, espere até que não `postgres01-external-svc` apareça na saída do comando kubectl `kubectl get svc -n arc` .
 - Carregar a página de visão geral e a página de configuração do Computação + Armazenamento no Azure Data Studio é lento. 



## <a name="next-steps"></a>Passos seguintes
  
> **Só quer experimentar as coisas?**  
> Inicie-se rapidamente com [o Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) no Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM.

[Instale as ferramentas do cliente](install-client-tools.md)

[Criar o controlador de dados Azure Arc](create-data-controller.md) (requer a instalação das ferramentas do cliente primeiro)

[Criar um exemplo gerido pelo Azure SQL em Azure Arc](create-sql-managed-instance.md) (requer a criação de um controlador de dados Azure Arc primeiro)

[Criar uma base de dados Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc](create-postgresql-hyperscale-server-group.md) (requer a criação de um controlador de dados Azure Arc primeiro)
