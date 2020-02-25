---
title: Use a Caixa de Dados Azure Heavy para mover conteúdo de partilha de ficheiros para O SharePoint Online
description: Use este tutorial para aprender a migrar conteúdo de partilha de ficheiros para Partilhar O Ponto Online usando a sua Caixa de Dados Azure Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: f97ea17551d4415f7ed6371853172cfde30fe4b6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560053"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Utilize a Caixa de Dados Azure Heavy para migrar o seu conteúdo de partilha de ficheiros para o SharePoint Online

Utilize a sua Caixa de Dados Azure Heavy e a Ferramenta de Migração SharePoint (SPMT) para migrar facilmente o conteúdo da partilha de ficheiros para o SharePoint Online e oneDrive. Ao utilizar a Data Box Heavy, pode remover a dependência da sua ligação de rede de área ampla (WAN) para transferir os dados.

O Microsoft Azure Data Box é um serviço que permite encomendar um dispositivo a partir do portal Microsoft Azure. Em seguida, pode copiar terabytes de dados dos seus servidores para o dispositivo. Depois de enviá-lo de volta para a Microsoft, os seus dados são copiados para o Azure. Dependendo do tamanho dos dados que pretende transferir, pode escolher entre:

- [Disco de caixa](https://docs.microsoft.com/azure/databox/data-box-disk-overview) de dados com capacidade utilizável de 35 TB por encomenda para conjuntos de dados de pequeno a médio.º.
- [Caixa](https://docs.microsoft.com/azure/databox/data-box-overview) de dados com capacidade utilizável de 80 TB por dispositivo para conjuntos de dados médios a grandes.
- [Caixa de dados Pesada](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) com capacidade utilizável de 770 TB por dispositivo para grandes conjuntos de dados.

Este artigo fala especificamente sobre como usar a Caixa de Dados Heavy para migrar o seu conteúdo de partilha de ficheiros para o SharePoint Online.

## <a name="requirements-and-costs"></a>Requisitos e custos

### <a name="for-data-box-heavy"></a>Para Data Box Heavy

- Data Box Heavy só está disponível para ofertas de patrocínio solução de empresa (EA), Cloud (CSP) ou Azure. Se a sua subscrição não cair em nenhum dos tipos acima referidos, contacte o Microsoft Support para atualizar a sua subscrição ou ver preços de [subscrição do Azure](https://azure.microsoft.com/pricing/).
- Há uma taxa para usar data box heavy. Certifique-se de rever os [preços pesados](https://azure.microsoft.com/pricing/details/databox/heavy/)da Caixa de Dados .


### <a name="for-sharepoint-online"></a>Para SharePoint Online

- Reveja os [requisitos mínimos para a Ferramenta de Migração SharePoint (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

Este fluxo de trabalho requer que realize etapas na Caixa de Dados Azure Heavy, bem como no SharePoint Online.
Os seguintes passos dizem respeito à sua Caixa de Dados Azure Heavy.

1. Encomende caixa de dados Azure Pesada.
2. Receba e instale o seu dispositivo.
3. Copie os dados da partilha de ficheiros no local para pasta para Ficheiros Azure no seu dispositivo.
4. Depois de a cópia estar completa, envie o dispositivo de volta de acordo com as instruções.
5. Aguarde que os dados sejam completamente enviados para o Azure.

Os seguintes passos dizem respeito ao SharePoint Online.

6. Crie um VM no portal Azure e monte a partilha de ficheiros Azure nele.
7. Instale a ferramenta SPMT no Azure VM.
8. Executar a ferramenta SPMT utilizando a partilha de ficheiros Azure como *fonte*.
9. Complete os passos finais da ferramenta.
10. Verifique e confirme os seus dados.

## <a name="use-data-box-heavy-to-copy-data"></a>Utilizar caixa de dados pesada para copiar dados

Tome os seguintes passos para copiar dados para a sua Caixa de Dados Pesada.

1. [Encomende a sua Caixa de Dados Pesada](data-box-heavy-deploy-ordered.md).
2. Depois de receber a sua Caixa de Dados Pesada, [instale a caixa de dados pesada](data-box-heavy-deploy-set-up.md). Vai saquear e configurar os dois nós do seu dispositivo.
3. [Copiar dados para Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). Durante a cópia, certifique-se de:

    - Utilize apenas a pasta *StorageAccountName_AzFile* na Caixa de Dados Pesada para copiar os dados. Isto porque pretende que os dados acabem numa partilha de ficheiros Azure, não em blocos ou bolhas de página.
    - Copiar ficheiros para uma pasta dentro *StorageAccountName_AzFile* pasta. Uma subpasta dentro *StorageAccountName_AzFile* pasta cria uma partilha de ficheiros. Os ficheiros copiados diretamente para *StorageAccountName_AzFile* falha da pasta e são carregados como bolhas de bloco. Esta é a partilha de ficheiros que vai montar no seu VM no próximo passo.
    - Copie os dados para ambos os nós da sua Caixa de Dados Pesado.
3. [Prepare-se para enviar](data-box-heavy-deploy-picked-up.md#prepare-to-ship) o seu dispositivo. Um preparado bem sucedido para enviar garante um carregamento bem-sucedido de ficheiros para o Azure.
4. [Devolva o dispositivo](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Verifique o upload de dados para o Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Use SPMT para migrar dados

Depois de receber a confirmação da equipa de dados do Azure de que a sua cópia de dados já foi concluída, proceda à migração dos seus dados para o SharePoint Online.

Para melhor desempenho e conectividade, recomendamos que crie uma Máquina Virtual Azure (VM).

1. Assine no portal Azure [e,](../virtual-machines/windows/quick-create-portal.md)em seguida, crie uma máquina virtual .
2. [Monte a parte de ficheiro Azure no VM](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Descarregue a ferramenta SharePoint Migration](https://spmtreleasescus.blob.core.windows.net/install/default.htm) e instale-a no seu Azure VM.
4. Inicie a ferramenta de migração SharePoint. Clique **em Iniciar sessão** e insira o seu nome de utilizador e senha do Office 365.
5. Quando solicitado **Onde estão os seus dados?** Introduza o caminho para a partilha de ficheiros Do Seu Azure onde os seus dados estão localizados.
6. Siga as restantes solicitações normalmente, incluindo a localização do alvo. Para mais informações, vá [a Como utilizar a Ferramenta de Migração SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - A velocidade a que os dados são ingeridos no SharePoint Online é afetada por vários fatores, independentemente de já ter os seus dados no Azure. Compreender estes fatores irá ajudá-lo a planear e maximizar a eficiência da sua migração.  Para mais informações, vá ao [SharePoint Online e](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed)à OneDrive velocidade de migração .
> - Existe o risco de perder permissões existentes em ficheiros ao migrar os dados para o SharePoint Online. Também pode perder certos metadados, tais como *Criado por* e *Data modificados por*.

## <a name="next-steps"></a>Passos seguintes

[Encomende a sua Caixa de Dados Pesada](./data-box-heavy-deploy-ordered.md)