---
title: Use a Azure Data Box Heavy para mover o conteúdo da partilha de ficheiros para o SharePoint Online
description: Utilize este tutorial para aprender a migrar conteúdos de partilha de ficheiros para Partilhar Online usando a sua Caixa de Dados Azure Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: b00a944b69b8492d4aa00258154bf00f1c66ad33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92127119"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Utilize a Caixa de Dados Azure Heavy para migrar o conteúdo da partilha de ficheiros para o SharePoint Online

Utilize a sua Caixa de Dados Azure Heavy e a Ferramenta de Migração SharePoint (SPMT) para migrar facilmente o conteúdo da partilha de ficheiros para SharePoint Online e OneDrive. Ao utilizar a Data Box Heavy, pode remover a dependência da sua ligação de rede de área larga (WAN) para transferir os dados.

O Microsoft Azure Data Box é um serviço que permite encomendar um dispositivo a partir do portal Microsoft Azure. Em seguida, pode copiar terabytes de dados dos seus servidores para o dispositivo. Depois de enviá-lo de volta para a Microsoft, os seus dados são copiados para o Azure. Dependendo do tamanho dos dados que pretende transferir, pode escolher entre:

- [Disco de caixa de dados](./data-box-disk-overview.md) com capacidade utilizável de 35-TB por encomenda para conjuntos de dados pequenos a médios.
- [Caixa de dados](./data-box-overview.md) com capacidade utilizável de 80-TB por dispositivo para conjuntos de dados médios a grandes.
- [Caixa de dados Pesada](./data-box-heavy-overview.md) com capacidade utilizável de 770 TB por dispositivo para grandes conjuntos de dados.

Este artigo fala especificamente sobre como usar a Caixa de Dados Pesada para migrar o conteúdo da partilha de ficheiros para o SharePoint Online.

## <a name="requirements-and-costs"></a>Requisitos e custos

### <a name="for-data-box-heavy"></a>Para Data Box Heavy

- Data Box Heavy só está disponível para ofertas de patrocínios do Enterprise Agreement (EA), Cloud Solution provider (CSP) ou Azure. Se a sua subscrição não cair em nenhum dos tipos acima, contacte o Microsoft Support para atualizar a sua subscrição ou ver [os preços de subscrição do Azure](https://azure.microsoft.com/pricing/).
- Há uma taxa para usar data box pesado. Certifique-se de rever os [preços pesados da Caixa de Dados](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Para SharePoint Online

- Reveja os [requisitos mínimos para a Ferramenta de Migração SharePoint (SPMT)](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

Este fluxo de trabalho requer que execute etapas na Caixa de Dados Azure Pesada, bem como no SharePoint Online.
Os seguintes passos dizem respeito à sua Caixa de Dados Azure Heavy.

1. Encomende caixa de dados Azure pesada.
2. Receba e instale o seu dispositivo.
3. Copie os dados da partilha de ficheiros no local para a pasta de Ficheiros Azure no seu dispositivo.
4. Depois de concluída a cópia, volte a enviar o dispositivo de acordo com as instruções.
5. Aguarde que os dados carreguem completamente para a Azure.

Os seguintes passos dizem respeito ao SharePoint Online.

6. Crie um VM no portal Azure e monte a partilha de ficheiros Azure nele.
7. Instale a ferramenta SPMT no Azure VM.
8. Executar a ferramenta SPMT utilizando a partilha de ficheiros Azure como *fonte*.
9. Complete os passos finais da ferramenta.
10. Verifique e confirme os seus dados.

## <a name="use-data-box-heavy-to-copy-data"></a>Use a caixa de dados pesada para copiar dados

Tome os seguintes passos para copiar dados para a sua Caixa de Dados Pesada.

1. [Encomende a sua Caixa de Dados Pesada](data-box-heavy-deploy-ordered.md).
2. Depois de receber a sua Caixa de Dados Pesada, [Configurar a Caixa de Dados Pesada](data-box-heavy-deploy-set-up.md). Irá configurar os dois nós no seu dispositivo.
3. [Copiar dados para Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). Enquanto copia, certifique-se de:

    - Utilize apenas a pasta *StorageAccountName_AzFile* na Caixa de Dados Pesada para copiar os dados. Isto porque pretende que os dados acabem numa partilha de ficheiros Azure, não em blobs de blocos ou em bolhas de página.
    - Copie os ficheiros para uma pasta *dentro StorageAccountName_AzFile* pasta. Uma sub-página dentro *de StorageAccountName_AzFile* pasta cria uma partilha de ficheiros. Os ficheiros copiados diretamente para *StorageAccountName_AzFile* falha de pasta e são carregados como bolhas de bloco. Esta é a partilha de ficheiros que irá montar no seu VM no próximo passo.
    - Copie os dados para ambos os nós da sua Caixa de Dados Pesada.
3. Corra [Prepare-se para enviar](data-box-heavy-deploy-picked-up.md#prepare-to-ship) o seu dispositivo. Uma preparação bem sucedida para enviar garante um carregamento bem sucedido de ficheiros para Azure.
4. [Devolva o aparelho](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Verifique o upload de dados para o Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Utilizar o SPMT para migrar dados

Depois de receber a confirmação da equipa de dados do Azure de que a sua cópia de dados foi concluída, proceda à migração dos seus dados para o SharePoint Online.

Para melhor desempenho e conectividade, recomendamos que crie uma Máquina Virtual Azure (VM).

1. Inscreva-se no portal Azure e, em seguida, [Crie uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).
2. [Monte a partilha de ficheiros Azure no VM](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Descarregue a ferramenta De migração SharePoint](https://spmtreleasescus.blob.core.windows.net/install/default.htm) e instale-a no seu Azure VM.
4. Inicie a Ferramenta de Migração SharePoint. Clique **em Iniciar sômed** e insira o nome de utilizador e a palavra-passe para o seu trabalho ou conta escolar.
5. Quando solicitado **Onde estão os seus dados?**  Insira o caminho para a sua partilha de ficheiros Azure onde os seus dados estão localizados.
6. Siga as indicações restantes normalmente, incluindo a localização do alvo. Para mais informações, [aceda a Como utilizar a Ferramenta de Migração SharePoint](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - A velocidade a que os dados são ingeridos no SharePoint Online é afetada por vários fatores, independentemente de já ter os seus dados no Azure. Compreender estes fatores irá ajudá-lo a planear e maximizar a eficiência da sua migração.  Para mais informações, aceda ao [SharePoint Online e à Velocidade de migração OneDrive.](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed)
> - Existe o risco de perder permissões existentes em ficheiros ao migrar os dados para o SharePoint Online. Pode também perder certos metadados, tais como *Criado por* e *Data modificado por*.

## <a name="next-steps"></a>Passos seguintes

[Encomende a sua Caixa de Dados Pesada](./data-box-heavy-deploy-ordered.md)