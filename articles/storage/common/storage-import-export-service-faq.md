---
title: FAQ do serviço importar/exportar do Azure | Documentos da Microsoft
description: Leitura respostas para perguntas mais frequentes sobre o serviço de exportação de importação do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/13/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ee2917c64843c8ab137e0122d63a328d6c19fedb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867580"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>O serviço importar/exportar do Azure: Perguntas mais frequentes 
Seguem-se perguntas e respostas que possa ter quando utiliza o serviço importar/exportar do Azure para transferir dados para o armazenamento do Azure. As perguntas e respostas estão organizadas pelas seguintes categorias:

- Sobre o serviço importar/exportar
- Preparar os discos para importar/exportar
- Tarefas de importação/exportação
- Discos de envio
- Diversos 

## <a name="about-importexport-service"></a>Sobre o serviço importar/exportar

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Pode copiar armazenamento de ficheiros do Azure com o serviço importar/exportar do Azure?

Sim. Importe o suporte do serviço importar/exportar do Azure para o armazenamento de ficheiros do Azure. Não suporta a exportação dos ficheiros do Azure neste momento.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>O serviço importar/exportar do Azure está disponível para subscrições de CSP?

Sim. Serviço de importação/exportação do Azure suporta subscrições de fornecedores de soluções Cloud (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Pode utilizar o serviço importar/exportar do Azure para copiar dados de SharePoint e de caixas de correio PST ao O365?

Sim. Para obter mais informações, aceda a [arquivos PST de importação ou dados do SharePoint para o Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Pode utilizar o serviço importar/exportar do Azure para copiar minhas cópias de segurança offline para o serviço de cópia de segurança do Azure?

Sim. Para obter mais informações, aceda a [fluxo de trabalho de cópia de segurança Offline na cópia de segurança do Azure](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Pode comprar unidades para tarefas de importação/exportação da Microsoft?

Não. Precisa envie as suas próprias unidades para importar e exportar tarefas.


## <a name="preparing-disks-for-importexport"></a>A preparar discos para importar/exportar

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Pode ignorar o passo de preparação de unidade para uma tarefa de importação? Pode preparar uma unidade sem copiar?

Não. Qualquer unidade utilizada para importar os dados deve estar preparada usando a ferramenta de WAImportExport do Azure. Utilize a ferramenta também copiar dados para a unidade.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>É necessário efetuar qualquer preparação do disco durante a criação de uma tarefa de exportação?

Não. Recomenda-se alguns prechecks. Para verificar o número de discos necessários, utilize o comando de PreviewExport da ferramenta WAImportExport. Para obter mais informações, consulte [pré-visualização da utilização de unidade para uma tarefa de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). O comando ajuda-o a visualizar a utilização da unidade para os blobs selecionados, com base no tamanho das unidades de que se pretende usar. Verifique também que pode ler e gravar no disco rígido que é enviado para a tarefa de exportação.

## <a name="importexport-jobs"></a>Tarefas de importação/exportação

### <a name="can-i-cancel-my-job"></a>Pode cancelar a minha tarefa?
Sim. Pode cancelar uma tarefa quando o respetivo estado é **Creating** ou **envio**. Além desses estágios, não é possível cancelar a tarefa e ele continua até a fase final.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>O tempo que pode ver o estado das tarefas concluídas no portal do Azure?
Pode ver o estado de tarefas concluídas durante 90 dias. Tarefas concluídas são eliminadas passados 90 dias.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?
Uma importação ou a tarefa de exportação pode referenciar apenas 10 unidades numa única tarefa. Para enviar mais de 10 unidades, deve criar várias tarefas. Unidades associadas à mesma tarefa devem ser enviadas em conjunto no mesmo pacote. Para obter mais informações e orientações que quando a capacidade de dados abrange vários disco importar tarefas, contacte a Microsoft em bulkimport@microsoft.com. 

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>O blob carregado mostra o estado como "Expirada de concessão". O que devo fazer?
Pode ignorar o campo "A concessão expirou". Importar/exportar demora concessão no blob durante o carregamento para se certificar de que nenhum outro processo pode atualizar o blob em paralelo. Concessão expirado implica que importar/exportar já não está a carregar a ele e o blob está disponível para utilização. 

## <a name="shipping-disks"></a>Discos de envio

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>O que é o número máximo de HDD numa remessa?
Não existe nenhum limite para o número de HDDs numa remessa. Se os discos pertencem a vários trabalhos, recomendamos que: 
- os discos com os nomes das tarefas correspondente da etiqueta.
- atualizar as tarefas com um número de controlo com -1, o sufixo-2 etc.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Posso incluir qualquer coisa que não seja o HDD no meu pacote?
Envie apenas os discos rígidos no pacote de envio. Não incluem itens como cabos de alimentação de energia ou de cabos USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>É necessário que enviar as minhas unidades usando FedEx ou DHL?
Pode enviar unidades para o datacenter do Azure através de qualquer operadora conhecida como FedEx, DHL, UPS ou nos serviço Postal. No entanto, para devolução de unidades para do Centro de dados, tem de fornecer:

- Um número de conta FedEx nos EUA e UE, ou
- Um número de conta DHL nas regiões Ásia e Austrália.

> [!NOTE]
> Os datacenters na Índia requer uma letra de declaração no seu letterhead (entrega challan) para retornar as unidades. Para organizar a passagem de entrada necessária, também tem de reservar a escolher a cópia de segurança com a sua operadora selecionada e partilhar os detalhes com o Centro de dados.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Existem restrições com envio minha unidade internacionalmente?
Tenha em atenção que o suporte de dados físicos que são de envio poderá ter de cruzar limites internacionais. É responsável por assegurar que sua mídia física e os dados são importados e/ou exportados de acordo com as leis aplicáveis. Antes do envio a mídia física, verifique com seu assistentes para verificar que o suporte de dados e os dados podem ser legalmente enviados para o Centro de dados identificado. Isto irá ajudar a garantir que atinge a Microsoft em tempo hábil.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Existem requisitos especiais para o fornecimento de meus discos para um datacenter?

Os requisitos dependem as restrições de datacenter do Azure específicas.
- Há alguns sites, que exigem um número de ID de entrada a ser escrito sobre parcel por motivos de segurança do datacenter da Microsoft. Antes do envio suas unidades ou os discos para o Centro de dados, entre em contato com operações do Azure data box (adbops@microsoft.com) para resolver este número. Sem este número, o pacote será rejeitado.
- Os datacenters na Índia requerem os detalhes pessoais do controlador, como o cartão de identificação de governo ou não de prova. (por exemplo, PAN, AADHAR, DL), nome, contato e o carro prato número para obter uma passagem de entrada de porta. Para evitar atrasos de entrega, informe a sua operadora sobre estes requisitos.


### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Ao criar uma tarefa, o endereço de envio é um local diferente da minha localização da conta de armazenamento. O que devo fazer?

Algumas localizações de conta de armazenamento são mapeadas para as localizações de envio de alternativas. Anteriormente disponíveis localizações de envio podem também ser temporariamente mapeadas para locais alternativos. Verifique sempre o endereço de envio fornecido durante a criação da tarefa antes do envio suas unidades.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Quando a minha unidade de envio, a deteção de carrier solicita o data center contacto endereço e número de telefone. O que posso fornecer?

O número de telefone e o controlador de domínio endereço é fornecido como parte da criação da tarefa.


## <a name="miscellaneous"></a>Diversos

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>O que acontece se eu envio acidentalmente um HDD que não estão em conformidade com os requisitos de suporte?

O Centro de dados do Azure irá devolver a unidade que não estão em conformidade com os requisitos de suporte para. Se apenas algumas das unidades no pacote de cumprem os requisitos de suporte, essas unidades serão processadas e as unidades que não cumprem os requisitos serão retornadas para.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>O serviço formatar as unidades antes de retorná-los?

Não. Todas as unidades são encriptadas com o BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Como posso aceder a dados que são importados por este serviço?

Utilizar o Portal do Azure ou [Explorador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) para acessar os dados na sua conta de armazenamento do Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Após a importação estiver concluída, o que meus dados como na conta de armazenamento? Minha hierarquia de diretório é mantida?

Quando preparar um disco rígido para uma tarefa de importação, o destino é especificado pelo campo DstBlobPathOrPrefix no conjunto de dados CSV. Este é o contentor de destino na conta de armazenamento para o qual os dados do disco rígido são copiados. Dentro deste contentor de destino, diretórios virtuais são criados para pastas a partir do disco rígido e blobs são criados para os ficheiros. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Se uma unidade tem ficheiros que já existem na minha conta de armazenamento, o serviço substituir blobs existentes ou ficheiros?

Depende. Quando preparar a unidade, pode especificar se os ficheiros de destino devem ser substituídos ou ignorado usando o campo no ficheiro CSV de conjunto de dados chamado disposição: < mudar o nome | não-substituir | substituir >. Por predefinição, o serviço muda o nome de novos ficheiros em vez de substituir blobs existentes ou ficheiros.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>A ferramenta de WAImportExport é compatível com sistemas operativos de 32 bits?
Não. A ferramenta de WAImportExport só é compatível com sistemas de operativos do Windows de 64 bits. Para obter uma lista completa de SO suportado, aceda a [sistemas operativos suportados](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Qual é o Blob de blocos e o tamanho do Blob de página suportados pelo Azure importar/exportar máximo?

Tamanho do Blob de blocos de Max é aproximadamente 4.768TB ou 5,000,000 MB.
Tamanho do Blob de páginas máxima é de 8TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Importar/exportar do Azure suporta a encriptação AES-256?
O serviço importar/exportar do Azure utiliza a encriptação do bitlocker de AES-128 por predefinição. Pode alterar isto para AES-256 criptografando manualmente com o bitlocker antes dos dados são copiados. 

- Se utilizar [WAImportExport V1](https://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), segue-se um comando de exemplo
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Se utilizar [WAImportExport V2](https://www.microsoft.com/download/details.aspx?id=55280) especificar "AlreadyEncrypted" e fornecer a chave no driveset CSV.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Passos Seguintes

* [O que é importar/exportar do Azure?](storage-import-export-service.md)


