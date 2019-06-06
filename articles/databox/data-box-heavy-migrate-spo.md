---
title: Utilizar pesadas de caixa de dados do Azure para migrar o conteúdo de partilha do ficheiro ao SharePoint Online | Documentos da Microsoft
description: Utilize este tutorial para saber como migrar conteúdos da partilha de ficheiro para partilhar ponto Online com sua pesadas de caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 06/05/2019
ms.author: alkohli
ms.openlocfilehash: 5628a1b3ea42c91f49f78699c37bb2b306275e9e
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730848"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Utilize o pesadas de caixa de dados do Azure para migrar os seus conteúdos de partilha de ficheiro ao SharePoint Online

Utilize o seu pesada de caixa de dados do Azure e a ferramenta de migração do SharePoint (SPMT) para migrar facilmente seu conteúdo de partilha de ficheiros para o SharePoint Online e OneDrive. Ao utilizar dados de caixa pesadas, pode remover a dependência na sua ligação de rede (WAN) de longa distância para transferir os dados.

O Microsoft Azure Data Box é um serviço que permite-lhe solicitar um dispositivo a partir do portal do Microsoft Azure. Em seguida, pode copiar terabytes de dados dos seus servidores para o dispositivo. Após o lançamento-lo à Microsoft, os seus dados são copiados para o Azure. Dependendo do tamanho dos dados que pretende transferir, pode escolher entre:

- [Disco data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) com capacidade utilizável de 35 TB por ordem para conjuntos de dados de pequeno a médio.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) com capacidade utilizável de 80 TB por dispositivo para conjuntos de dados de médio a grande.
- [Dados de caixa pesada](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) com capacidade utilizável de 770 TB por dispositivo para grandes conjuntos de dados. Pesadas de caixa de dados está atualmente em pré-visualização.

Este artigo fala-se especificamente sobre como utilizar o pesadas de caixa de dados para migrar os seus conteúdos de partilha de ficheiro ao SharePoint Online.

## <a name="requirements-and-costs"></a>Requisitos e custos

### <a name="for-data-box-heavy"></a>Para mais de caixa de dados

- Dados de caixa pesada só está disponível para contrato Enterprise (EA), o fornecedor de soluções Cloud (CSP), ou ofertas de patrocínio do Azure. Se a sua subscrição não se encontra em qualquer um dos tipos acima, entre em contato com Support da Microsoft para atualizar a sua subscrição ou veja [preço de subscrição do Azure](https://azure.microsoft.com/pricing/).
- Existe uma taxa para usar dados de caixa pesada. Certificar-se de que reveja os [preços dados caixa pesada](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Para o SharePoint Online

- Reveja os [os requisitos mínimos para a ferramenta de migração do SharePoint (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Descrição geral do fluxo de trabalho

Este fluxo de trabalho requer que execute passos no pesadas de caixa de dados do Azure e no SharePoint online.
Os passos seguintes estão relacionadas à sua pesadas de caixa de dados do Azure.

1. Ordem pesado de caixa de dados do Azure.
2. Receber e configurar o dispositivo.
3. Partilham dados de cópia do ficheiro no local para a pasta para ficheiros do Azure no seu dispositivo.
4. Depois da cópia estiver concluída, envie o dispositivo novamente de acordo com as instruções.
5. Aguarde que os dados para carregar completamente para o Azure.

Os seguintes passos estão relacionadas ao SharePoint online.

6. Criar uma VM no portal do Azure e Monte a partilha de ficheiros do Azure no mesmo.
7. Instale a ferramenta SPMT na VM do Azure.
8. Execute a ferramenta SPMT com a partilha de ficheiros do Azure como o *origem*.
9. Conclua as etapas finais da ferramenta.
10. Verifique e confirme os seus dados.

## <a name="use-data-box-heavy-to-copy-data"></a>Utilizar dados de caixa pesadas para copiar dados

Siga os passos seguintes para copiar dados para seu intenso de caixa de dados.

1. [Solicite sua pesado de caixa de dados](data-box-heavy-deploy-ordered.md).
2. Depois de receber pesado de caixa de seus dados, [configurar os dados de caixa pesada](data-box-heavy-deploy-set-up.md). Irá instalar e configurar ambos os nós no seu dispositivo.
3. [Copiar dados para a caixa de dados do Azure pesada](data-box-heavy-deploy-copy-data.md). Ao copiar, certifique-se de que:

    - Utilizar apenas o *AzureFile* pasta na pesadas de caixa de dados para copiar os dados. Isso é porque pretende que os dados terminar numa partilha de ficheiros do Azure, não em blobs de blocos ou blobs de páginas.
    - Copiar ficheiros para uma pasta dentro *AzureFile* pasta. Uma subpasta dentro *AzureFile* pasta cria uma partilha de ficheiros. Ficheiros copiados diretamente para o *AzureFile* falharão de pasta e são carregados como blobs de blocos. Esta é a partilha de ficheiros que irá montar na sua VM no próximo passo.
    - Copie dados para ambos os nós do seu intenso de caixa de dados.
3. Execute [preparação para envio](data-box-heavy-deploy-picked-up.md#prepare-to-ship) no seu dispositivo. Êxito de preparação para envio garante um carregamento bem-sucedido dos ficheiros para o Azure.
4. [Devolver o dispositivo](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Verifique se o carregamento de dados para o Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Utilizar SPMT para migrar dados

Depois de receber a confirmação da equipa de dados do Azure que concluiu a cópia de dados, agora, pode avançar para migrar os dados para o SharePoint Online.

Para obter melhor desempenho e conectividade, recomendamos que crie uma Máquina Virtual do Azure (VM).

1. Inicie sessão no portal do Azure e, em seguida [criar uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).
2. [Montar a partilha de ficheiros do Azure na VM](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Transferir a ferramenta de migração do SharePoint](http://spmtreleasescus.blob.core.windows.net/install/default.htm) e instalá-lo na sua VM do Azure.
4. Inicie a ferramenta de migração do SharePoint. Clique em **iniciar sessão** e introduza o nome de utilizador do Office 365 e a palavra-passe.
5. Quando lhe for pedido **onde estão os seus dados?** , selecione **partilha de ficheiros**. Introduza o caminho para a partilha de ficheiros do Azure onde estão localizados o seus dados.
6. Siga as instruções restantes como habitualmente, incluindo a localização de destino. Para obter mais informações, aceda a [como utilizar a ferramenta de migração do SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - A velocidade a que os dados são ingeridos no SharePoint Online é afetada por diversos fatores, independentemente se ainda tiver seus dados no Azure. Noções básicas sobre estes fatores irão ajudá-lo a planejar e maximizar a eficiência da sua migração.  Para obter mais informações, aceda a [migração do SharePoint Online e OneDrive velocidade](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Há um risco de perda de permissões existentes em ficheiros ao migrar os dados para o SharePoint Online. Também poderá perder alguns metadados, como *criadas pelo* e *data de modificação por*.

## <a name="next-steps"></a>Passos Seguintes

[Solicite sua pesado de caixa de dados](./data-box-heavy-deploy-ordered.md)