---
title: Perguntas frequentes sobre o serviço de importação/exportação do Azure | Microsoft Docs
description: Leia as respostas para perguntas frequentes sobre o serviço de exportação de importação do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/18/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 109f541157716ae4f9b195d0a3ed02a1d8c91960
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314112"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Serviço de importação/exportação do Azure: perguntas frequentes 
Veja a seguir as perguntas e respostas que você pode ter ao usar o serviço de importação/exportação do Azure para transferir dados para o armazenamento do Azure. As perguntas e respostas estão organizadas pelas seguintes categorias:

- Sobre o serviço de importação/exportação
- Preparando os discos para importação/exportação
- Trabalhos de importação/exportação
- Discos de envio
- Diversos 

## <a name="about-importexport-service"></a>Sobre o serviço de importação/exportação

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Posso copiar o armazenamento de arquivos do Azure usando o serviço de importação/exportação do Azure?

Sim. O serviço de importação/exportação do Azure dá suporte à importação no armazenamento de arquivos do Azure. Não há suporte para a exportação de arquivos do Azure neste momento.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>O serviço de importação/exportação do Azure está disponível para assinaturas do CSP?

Sim. O serviço de importação/exportação do Azure dá suporte a assinaturas de CSP (provedores de soluções de nuvem).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Posso usar o serviço de importação/exportação do Azure para copiar caixas de correio de PST e dados do SharePoint para o O365?

Sim. Para obter mais informações, acesse [importar arquivos PST ou dados do SharePoint para o Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Posso usar o serviço de importação/exportação do Azure para copiar meus backups offline para o serviço de backup do Azure?

Sim. Para obter mais informações, acesse [fluxo de trabalho de backup offline no backup do Azure](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Posso adquirir unidades para trabalhos de importação/exportação da Microsoft?

Não. Você precisa enviar suas próprias unidades para trabalhos de importação e exportação.


## <a name="preparing-disks-for-importexport"></a>Preparando discos para importação/exportação

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Posso ignorar a etapa de preparação da unidade para um trabalho de importação? Posso preparar uma unidade sem copiar?

Não. Qualquer unidade usada para importar dados deve ser preparada usando a ferramenta WAImportExport do Azure. Use a ferramenta para também copiar dados para a unidade.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>É necessário executar qualquer preparação de disco ao criar um trabalho de exportação?

Não. Algumas verificações são recomendadas. Para verificar o número de discos necessários, use o comando PreviewExport da ferramenta WAImportExport. Para obter mais informações, consulte [visualizando o uso da unidade para um trabalho de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). O comando ajuda a visualizar o uso da unidade para os BLOBs selecionados, com base no tamanho das unidades que você pretende usar. Verifique também se você pode ler e gravar no disco rígido que é enviado para o trabalho de exportação.

## <a name="importexport-jobs"></a>Trabalhos de importação/exportação

### <a name="can-i-cancel-my-job"></a>Posso cancelar meu trabalho?
Sim. Você pode cancelar um trabalho quando seu status está sendo **criado** ou **enviado**. Além desses estágios, o trabalho não pode ser cancelado e continua até o estágio final.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Por quanto tempo posso exibir o status dos trabalhos concluídos na portal do Azure?
Você pode exibir o status de trabalhos concluídos por até 90 dias. Os trabalhos concluídos são excluídos após 90 dias.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?
Um trabalho de importação ou exportação pode fazer referência a apenas 10 unidades em um único trabalho. Para enviar mais de 10 unidades, você deve criar vários trabalhos. As unidades associadas ao mesmo trabalho devem ser enviadas juntas no mesmo pacote. Para obter mais informações e diretrizes quando a capacidade de dados abrange vários trabalhos de importação de disco, entre em contato com Suporte da Microsoft. 

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>O blob carregado mostra o status como "concessão expirada". O que devo fazer?
Você pode ignorar o campo "concessão expirada". A importação/exportação usa a concessão no blob durante o carregamento para garantir que nenhum outro processo possa atualizar o blob em paralelo. A concessão expirada implica que a importação/exportação não está mais carregando nela e o blob está disponível para seu uso. 

## <a name="shipping-disks"></a>Discos de envio

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Qual é o número máximo de HDD para em uma remessa?
Não há nenhum limite para o número de HDDs em uma remessa. Se os discos pertencerem a vários trabalhos, recomendamos que você: 
- Rotule os discos com nomes de trabalho correspondentes.
- Atualize os trabalhos com um número de controle sufixado com-1,-2 etc.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Devo incluir algo diferente do HDD em meu pacote?
Envie apenas os discos rígidos no pacote de envio. Não inclua itens como cabos de fonte de alimentação ou cabos USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>É necessário enviar minhas unidades usando FedEx ou DHL?
Você pode enviar unidades para o datacenter do Azure usando qualquer portadora conhecida, como FedEx, DHL, UPS ou serviço postal dos EUA. No entanto, para devolver o envio de unidades a você do datacenter, você deve fornecer:

- Um número de conta do FedEx nos EUA e na UE ou
- Um número de conta da DHL nas regiões da Ásia e da Austrália.

> [!NOTE]
> Os data centers na Índia exigem uma letra de declaração em seu papel timbrado (Delivery Challan) para retornar as unidades. Para organizar a passagem de entrada necessária, você também deve marcar a seleção com a transportadora selecionada e compartilhar os detalhes com o datacenter.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Há alguma restrição para enviar minha unidade internacionalmente?
Observe que a mídia física que você está enviando pode precisar cruzar bordas internacionais. Você é responsável por garantir que a mídia física e os dados sejam importados e/ou exportados de acordo com as leis aplicáveis. Antes de enviar a mídia física, verifique com seus consultores para verificar se a mídia e os dados podem ser enviados legalmente para o data center identificado. Isso ajudará a garantir que ele chegue à Microsoft em tempo hábil.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Há algum requisito especial para entregar meus discos a um datacenter?

Os requisitos dependem das restrições específicas do datacenter do Azure.
- Há alguns sites, que exigem que um número de ID de entrada do datacenter da Microsoft seja gravado na remessa por motivos de segurança. Antes de enviar suas unidades ou discos para o datacenter, entre em contato com oadbops@microsoft.comAzure data Box Operations () para obter esse número. Sem esse número, o pacote será rejeitado.
- Os data centers na Índia exigem os detalhes pessoais do driver, como o cartão de ID do governo ou prova de não. (por exemplo, PAN, AADHAR, DL), nome, contato e número da chapa do carro para obter uma passagem de entrada de portão. Para evitar atrasos de entrega, informe a sua operadora sobre esses requisitos.


### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Ao criar um trabalho, o endereço de envio é um local diferente do local da conta de armazenamento. O que devo fazer?

Alguns locais de conta de armazenamento são mapeados para locais de remessa alternativos. Os locais de remessa disponíveis anteriormente também podem ser mapeados temporariamente para locais alternativos. Sempre verifique o endereço de envio fornecido durante a criação do trabalho antes de enviar suas unidades.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Ao enviar a unidade, a transportadora solicita o endereço de contato data center e o número de telefone. O que devo fornecer?

O número de telefone e o endereço DC são fornecidos como parte da criação do trabalho.


## <a name="miscellaneous"></a>Diversos

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>O que acontece se eu enviar acidentalmente um HDD que não está em conformidade com os requisitos com suporte?

O data center do Azure retornará a unidade que não está de acordo com os requisitos com suporte para você. Se apenas algumas das unidades no pacote atenderem aos requisitos de suporte, essas unidades serão processadas e as unidades que não atenderem aos requisitos serão retornadas para você.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>O serviço formata as unidades antes de retorná-las?

Não. Todas as unidades são criptografadas com o BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Como posso acessar os dados que são importados por esse serviço?

Use o portal do Azure ou [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) para acessar os dados em sua conta de armazenamento do Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Depois que a importação for concluída, o que meus dados parecerão na conta de armazenamento? Minha hierarquia de diretório é preservada?

Ao preparar um disco rígido para um trabalho de importação, o destino é especificado pelo campo DstBlobPathOrPrefix no CSV de DataSet. Esse é o contêiner de destino na conta de armazenamento para o qual os dados do disco rígido são copiados. Dentro desse contêiner de destino, os diretórios virtuais são criados para pastas do disco rígido e os BLOBs são criados para arquivos. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Se uma unidade tiver arquivos que já existem na minha conta de armazenamento, o serviço substituirá os BLOBs ou arquivos existentes?

Dependem. Ao preparar a unidade, você pode especificar se os arquivos de destino devem ser substituídos ou ignorados usando o campo no arquivo CSV do conjunto de campos chamado disposição: < renomear | não-substituir | substituir >. Por padrão, o serviço renomeia os novos arquivos em vez de substituir os BLOBs ou arquivos existentes.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>A ferramenta WAImportExport é compatível com sistemas operacionais de 32 bits?
Não. A ferramenta WAImportExport é compatível apenas com sistemas operacionais Windows de 64 bits. Para obter uma lista completa de sistema operacional com suporte, vá para [sistemas operacionais com suporte](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Qual é o tamanho máximo do blob de blocos e do blob de páginas com suporte pela importação/exportação do Azure?

O tamanho máximo do blob de blocos é de aproximadamente 4.768 TB ou 5 milhões MB.
O tamanho máximo de blob de páginas é 8 TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>A importação/exportação do Azure dá suporte à criptografia AES-256?
O serviço de importação/exportação do Azure usa a criptografia BitLocker AES-128 por padrão. Você pode alterar isso para AES-256 criptografando manualmente com o BitLocker antes de os dados serem copiados. 

- Se estiver usando o [WAImportExport v1](https://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), abaixo está um comando de exemplo
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Se estiver usando [WAImportExport v2](https://www.microsoft.com/download/details.aspx?id=55280) , especifique "AlreadyEncrypted" e forneça a chave no CSV do driveset.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Passos Seguintes

* [O que é importação/exportação do Azure?](storage-import-export-service.md)


