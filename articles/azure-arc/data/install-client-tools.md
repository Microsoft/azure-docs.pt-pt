---
title: Instalar ferramentas de cliente
description: Instale azdata, kubectl, Azure CLI, psql, Azure Data Studio (Insiders) e a extensão arc para Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d61bc99e851b28712262dba8512c06b6e8872c0e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108223"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Instalar as ferramentas de cliente para implementar e gerir serviços de dados preparados para o Azure Arc

> [!IMPORTANT]
> Se estiver a atualizar para um novo lançamento mensal, certifique-se de atualizar também para a versão mais recente do Azure Data Studio, a ferramenta Azure Data CLI (azdata) e as extensões Azure Data CLI e Azure Arc para O Azure Data Studio.

Este documento percorre os passos para a instalação do Azure Data CLI (azdata), Azure Data Studio, Azure CLI (az) e a ferramenta CLI (kubectl) da Kubernetes na sua máquina cliente.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Ferramentas para criar e gerir serviços de dados aZure Arc 

A tabela que se segue lista as ferramentas comuns necessárias para criar e gerir os serviços de dados do Azure Arc e como instalar essas ferramentas:

| Ferramenta | Obrigatório | Descrição | Instalação |
|---|---|---|---|
| Azure Data CLI (azdata) | Sim | Ferramenta de linha de comando para instalar e gerir um grande cluster de dados. O Azure Data CLI também inclui um utilitário de linha de comando para ligar e consultar instâncias do Azure SQL e sql Server e servidores postgres usando os comandos `azdata sql query` (executar uma única consulta a partir da linha de comando), `azdata sql shell` (uma concha interativa) `azdata postgres query` e `azdata postgres shell` . | [Instalar](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Sim | Ferramenta de experiência rica para conectar e consultar uma variedade de bases de dados, incluindo Azure SQL, SQL Server, PostrgreSQL e MySQL. As extensões ao Azure Data Studio proporcionam uma experiência de administração para serviços de dados habilitados a Azure Arc. | [Instalar](/sql/azure-data-studio/download-azure-data-studio) |
| Extensão CLI de dados Azure para Azure Data Studio | Sim | Extensão para Azure Data Studio que irá instalar Azure Data CLI se ainda não o tiver.| Instale a partir da galeria de extensões no Azure Data Studio.|
| Extensão Azure Arc para Azure Data Studio | Sim | Extensão para Azure Data Studio que proporciona uma experiência de gestão para serviços de dados ativados a Azure Arc. Existe uma dependência da extensão Azure Data CLI para o Azure Data Studio. | Instale a partir da galeria de extensões no Azure Data Studio.|
| Extensão Pós-SQL no Azure Data Studio | Não | Extensão PostgreSQL para Azure Data Studio que fornece capacidades de gestão para PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Instale a partir da galeria de extensões no Azure Data Studio.|
| Azure CLI (az)<sup>1</sup> | Sim | Interface moderna de linha de comando para gerir serviços Azure. Usado com implementações AKS e para carregar Azure Arc permitiu o inventário de serviços de dados e dados de faturação para a Azure. (Mais[informações).](/cli/azure/?view=azure-cli-latest&preserve-view=true) | [Instalar](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Kubernetes CLI (kubectl)<sup>2</sup> | Sim | Ferramenta de linha de comando para gerir o cluster Kubernetes[(Mais informações).](https://kubernetes.io/docs/tasks/tools/install-kubectl/) | [Janelas](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| caracóis <sup>3</sup> | Necessário para alguns scripts de amostra. | Ferramenta de linha de comando para transferência de dados com URLs. | [Janelas](https://curl.haxx.se/windows/) \| Linux: instalar pacote de caracóis |
| oc | Obrigatório para as implementações OpenShift e Azure Redhat OpenShift. |`oc` é a interface da linha de comando Open Shift (CLI). | [Instalação do CLI](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> Deve utilizar a versão 2.0.4 ou posterior do Azure CLI. Corra `az --version` para encontrar a versão, se necessário.

<sup>2</sup> Deve utilizar a `kubectl` versão 1.13 ou posterior. Além disso, a versão de `kubectl` deve ser mais ou menos uma versão menor do seu cluster Kubernetes. Se pretender instalar uma versão específica no `kubectl` cliente, consulte [instalar binário via `kubectl` curl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (no Windows 10, utilize cmd.exe e não o Windows PowerShell para executar o curl).

<sup>3</sup> Se estiver a utilizar o PowerShell, o caracol é um pseudónimo do Invoke-WebRequest cmdlet.

## <a name="next-steps"></a>Passos seguintes

[Criar o controlador de dados Azure Arc](create-data-controller.md)