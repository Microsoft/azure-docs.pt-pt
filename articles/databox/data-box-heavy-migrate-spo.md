---
title: Use Azure Data Box Heavy para migrar o conteúdo do compartilhamento de arquivos para o SharePoint Online | Microsoft Docs
description: Use este tutorial para aprender a migrar o conteúdo do compartilhamento de arquivos para o compartilhamento online usando seu Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: 4955b28dff3193a95950912562cc3b6ec789479d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325275"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Usar o Azure Data Box Heavy para migrar o conteúdo do compartilhamento de arquivos para o SharePoint Online

Use o Azure Data Box Heavy e a ferramenta de migração do SharePoint (SPMT) para migrar facilmente o conteúdo do compartilhamento de arquivos para o SharePoint Online e o OneDrive. Usando Data Box Heavy, você pode remover a dependência em seu link de WAN (rede de longa distância) para transferir os dados.

O Microsoft Azure Data Box é um serviço que permite solicitar um dispositivo da portal do Microsoft Azure. Em seguida, você pode copiar terabytes de dados de seus servidores para o dispositivo. Depois de enviá-lo de volta à Microsoft, seus dados são copiados para o Azure. Dependendo do tamanho dos dados que você pretende transferir, você pode escolher:

- [Disco do data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) com capacidade utilizável de 35 TB por ordem para conjuntos de pequenos para médios.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) com capacidade utilizável de 80 TB por dispositivo para conjuntos de grandes volumes de mídia.
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) com capacidade utilizável de 770 TB por dispositivo para grandes conjuntos de altos.

Este artigo fala especificamente sobre como usar o Data Box Heavy para migrar o conteúdo do compartilhamento de arquivos para o SharePoint Online.

## <a name="requirements-and-costs"></a>Requisitos e custos

### <a name="for-data-box-heavy"></a>Para Data Box Heavy

- Data Box Heavy só está disponível para Enterprise Agreement (EA), provedor de soluções na nuvem (CSP) ou ofertas de patrocínio do Azure. Se sua assinatura não se enquadrar em nenhum dos tipos acima, entre em contato com Suporte da Microsoft para atualizar sua assinatura ou confira [preços de assinatura do Azure](https://azure.microsoft.com/pricing/).
- Há uma taxa para usar Data Box Heavy. Certifique-se de examinar os [preços de data Box Heavy](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Para o SharePoint Online

- Examine os [requisitos mínimos para a ferramenta de migração do SharePoint (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

Este fluxo de trabalho requer que você execute etapas em Azure Data Box Heavy, bem como no SharePoint Online.
As etapas a seguir estão relacionadas ao seu Azure Data Box Heavy.

1. Order Azure Data Box Heavy.
2. Receber e configurar seu dispositivo.
3. Copie dados do seu compartilhamento de arquivos local para a pasta para arquivos do Azure em seu dispositivo.
4. Depois que a cópia for concluída, envie o dispositivo de volta de acordo com as instruções.
5. Aguarde até que os dados sejam carregados completamente no Azure.

As etapas a seguir estão relacionadas ao SharePoint Online.

6. Crie uma VM no portal do Azure e monte o compartilhamento de arquivos do Azure nela.
7. Instale a ferramenta SPMT na VM do Azure.
8. Execute a ferramenta SPMT usando o compartilhamento de arquivos do Azure como a *origem*.
9. Conclua as etapas finais da ferramenta.
10. Verifique e confirme seus dados.

## <a name="use-data-box-heavy-to-copy-data"></a>Usar Data Box Heavy para copiar dados

Execute as etapas a seguir para copiar dados para o Data Box Heavy.

1. [Ordene seu data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Depois de receber sua Data Box Heavy, [Configure o data Box Heavy](data-box-heavy-deploy-set-up.md). Você usará o cabo e configurará ambos os nós em seu dispositivo.
3. [Copiar dados para Azure data Box Heavy](data-box-heavy-deploy-copy-data.md). Ao copiar, certifique-se de:

    - Use somente a pasta *StorageAccountName_AzFile* no data Box Heavy para copiar os dados. Isso ocorre porque você deseja que os dados terminem em um compartilhamento de arquivos do Azure, não em blobs de blocos ou BLOBs de páginas.
    - Copie os arquivos para uma pasta na pasta *StorageAccountName_AzFile* . Uma subpasta na pasta *StorageAccountName_AzFile* cria um compartilhamento de arquivos. Os arquivos copiados diretamente para a pasta *StorageAccountName_AzFile* falham e são carregados como BLOBs de blocos. Esse é o compartilhamento de arquivos que você montará em sua VM na próxima etapa.
    - Copie dados para ambos os nós do seu Data Box Heavy.
3. Execute [preparação para o envio](data-box-heavy-deploy-picked-up.md#prepare-to-ship) em seu dispositivo. Uma preparação bem-sucedida para o envio garante um upload bem-sucedido de arquivos no Azure.
4. [Retorne o dispositivo](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Verifique o carregamento de dados no Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Usar o SPMT para migrar dados

Depois de receber a confirmação da equipe de dados do Azure que sua cópia de dados concluiu, vá para migrar seus dados para o SharePoint Online.

Para obter um melhor desempenho e conectividade, recomendamos que você crie uma VM (máquina virtual) do Azure.

1. Entre no portal do Azure e, em seguida, [crie uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).
2. [Monte o compartilhamento de arquivos do Azure na VM](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Baixe a ferramenta de migração do SharePoint](https://spmtreleasescus.blob.core.windows.net/install/default.htm) e instale-a em sua VM do Azure.
4. Inicie a ferramenta de migração do SharePoint. Clique em **entrar** e insira seu nome de usuário e senha do Office 365.
5. Quando for solicitado **onde estão seus dados?** , selecione **compartilhamento de arquivos**. Insira o caminho para o compartilhamento de arquivos do Azure onde os dados estão localizados.
6. Siga as solicitações restantes normalmente, incluindo o local de destino. Para obter mais informações, acesse [como usar a ferramenta de migração do SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - A velocidade na qual os dados são ingeridos no SharePoint Online é afetada por vários fatores, independentemente de você ter seus dados já no Azure. Entender esses fatores ajudará você a planejar e maximizar a eficiência de sua migração.  Para obter mais informações, acesse o [SharePoint Online e a velocidade de migração do onedrive](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Há um risco de perder as permissões existentes nos arquivos ao migrar os dados para o SharePoint Online. Você também pode perder determinados metadados, como *criado por* e *data de modificação por*.

## <a name="next-steps"></a>Passos seguintes

[Ordenar seu Data Box Heavy](./data-box-heavy-deploy-ordered.md)