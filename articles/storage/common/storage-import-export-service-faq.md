---
title: FAQ para serviço de importação/exportação Azure [ Microsoft Docs
description: Leia as respostas a perguntas frequentes sobre o serviço de exportação de importações de Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 904f93aa705a4e327f29bbec109bdf3b937f6c70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519487"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Serviço de Importação/Exportação Azure: perguntas frequentes

Seguem-se as perguntas e respostas que poderá ter quando utilizar o seu serviço Azure Import/Export para transferir dados para o armazenamento do Azure. As perguntas e respostas estão organizadas pelas seguintes categorias:

- Sobre o serviço de Importação/Exportação
- Preparação dos discos para importação/exportação
- Empregos de importação/exportação
- Discos de envio
- Diversos

## <a name="about-importexport-service"></a>Sobre o serviço de Importação/Exportação

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Posso copiar o armazenamento do Ficheiro Azure utilizando o serviço de importação/exportação Azure?

Sim. O serviço azure import/exportação suporta a importação para armazenamento de ficheiros Azure. Neste momento, não apoia a exportação de Ficheiros Azure.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>O serviço azure import/exportação está disponível para subscrições de CSP?

Sim. O serviço Azure Import/Export suporta subscrições de Fornecedores de Soluções Cloud (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Posso usar o serviço azure import/exportação para copiar caixas de correio PST e dados SharePoint para O365?

Sim. Para mais informações, vá a [importar ficheiros PST ou dados do SharePoint para o Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Posso usar o serviço azure import/exportação para copiar os meus backups offline para o Serviço de Backup Azure?

Sim. Para mais informações, vá ao [fluxo de trabalho offline backup em Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Posso comprar unidades para empregos de importação/exportação da Microsoft?

Não. Tens de enviar os teus próprios discos para trabalhos de importação e exportação.

## <a name="preparing-disks-for-importexport"></a>Preparação de discos para importação/exportação

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Posso ignorar o passo de preparação para um trabalho de importação? Posso preparar um disco sem copiar?

Não. Qualquer unidade utilizada para importar dados deve ser preparada utilizando a ferramenta Azure WAImportExport. Utilize a ferramenta para copiar dados para a unidade.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Preciso de fazer alguma preparação para o disco quando criar um trabalho de exportação?

Não. Alguns pré-cheques são recomendados. Para verificar o número de discos necessários, utilize o comando PreviewExport da ferramenta WAImportExport. Para mais informações, consulte A Utilização da Unidade de [Visualização para um Trabalho de Exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). O comando ajuda-o a pré-visualizar o uso da unidade para as bolhas selecionadas, com base no tamanho das unidades que vai utilizar. Verifique também se pode ler e escrever para o disco rígido que é enviado para o trabalho de exportação.

## <a name="importexport-jobs"></a>Empregos de importação/exportação

### <a name="can-i-cancel-my-job"></a>Posso cancelar o meu trabalho?

Sim. Pode cancelar um trabalho quando o seu estado estiver **A Criar** ou **Enviar.** Para além destas fases, o trabalho não pode ser cancelado e continua até à fase final.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Quanto tempo posso ver o estado dos postos de trabalho concluídos no portal Azure?

Pode ver o estado dos trabalhos concluídos até 90 dias. Os trabalhos concluídos são eliminados após 90 dias.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?

Um trabalho de importação ou exportação pode fazer referência apenas a 10 unidades num único trabalho. Para enviar mais de 10 unidades, deve criar vários empregos. As unidades associadas ao mesmo trabalho devem ser enviadas juntas na mesma embalagem.
Para obter mais informações e orientação quando a capacidade de dados abranger vários postos de importação de discos, contacte o Microsoft Support.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>A bolha carregada mostra o estado de "Locação expirada". O que devo fazer?

Pode ignorar o campo "Arrendamento Expirado". A Importação/Exportação é arrendada à bolha durante o upload para garantir que nenhum outro processo possa atualizar a bolha paralelamente. O arrendamento Expirado implica que a Importação/Exportação já não está a ser carregada e a bolha está disponível para a sua utilização.

## <a name="shipping-disks"></a>Discos de envio

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Para que é o número máximo de HDD num carregamento?

Não há limite para o número de HDDs num só carregamento. Se os discos pertencerem a múltiplos trabalhos, recomendamos que:

- rotular os discos com os nomes de trabalho correspondentes.
- atualizar os trabalhos com um número de rastreio sufixo com -1, -2 etc.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Devo incluir algo além do HDD no meu pacote?

Envie apenas os seus discos rígidos no pacote de transporte. Não inclua itens como cabos de alimentação ou cabos USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Tenho de enviar os meus discos com fedEx ou DHL?

Pode enviar unidades para o centro de dados Azure utilizando qualquer transportadora conhecida como FedEx, DHL, UPS ou Us Postal Service. No entanto, para o envio de volta das unidades para si a partir do datacenter, deve fornecer:

- Um número de conta FedEx nos EUA e UE, ou
- Um número de conta DHL nas regiões da Ásia e austrália.

> [!NOTE]
> Os centros de dados na Índia exigem uma carta de declaração no seu papel timbrado (challan de entrega) para devolver os discos. Para organizar o passe de entrada necessário, deve também reservar a recolha com o seu transportador selecionado e partilhar os detalhes com o datacenter.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Há alguma restrição com o envio e a minha viagem internacionalmente?

Por favor, note que os meios de comunicação físicos que está a enviar podem precisar para atravessar fronteiras internacionais. É responsável por garantir que os seus meios físicos e dados são importados e/ou exportados de acordo com as leis aplicáveis. Antes de enviar os meios físicos, consulte os seus assessores para verificar se os seus meios e dados podem ser enviados legalmente para o centro de dados identificado. Isto ajudará a garantir que chega à Microsoft em tempo útil.

Após o upload estar concluído, o processo de devolução da unidade(s) a um endereço internacional pode demorar mais do que os 2-3 dias típicos necessários para o envio local. Durante a fase listada no portal Azure como Embalagem, a equipa da Data Box está a garantir que a documentação correta é fornecida para garantir que o envio está em conformidade com os vários requisitos internacionais de importação e exportação.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Há requisitos especiais para entregar os meus discos a um datacenter?

Os requisitos dependem das restrições específicas do centro de dados Azure.

- Existem alguns sites, como austrália, Alemanha e REINO UNIDO Sul, que exigem que um número de ID de entrada do Microsoft seja escrito na encomenda por razões de segurança. Antes de enviar as suas unidades ou discos para oadbops@microsoft.comdatacenter, contacte a Azure DataBox Operations () para obter este número. Sem este número, o pacote será rejeitado.
- Os centros de dados na Índia requerem os dados pessoais do condutor, como o Cartão de Identificação do Governo ou o Número de Prova. (por exemplo, PAN, AADHAR, DL), nome, contacto e o número da placa de carro para obter um passe de entrada do portão. Para evitar atrasos na entrega, informe a sua transportadora sobre estes requisitos.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Ao criar um emprego, o endereço de envio é um local diferente da minha conta de armazenamento. O que devo fazer?

Alguns locais da conta de armazenamento estão mapeados para locais de envio alternativos. Os locais de envio previamente disponíveis também podem ser temporariamente mapeados para locais alternativos. Verifique sempre o endereço de envio fornecido durante a criação de trabalho antes de enviar os seus discos.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Ao enviar a minha unidade, a transportadora pede o endereço de contacto do centro de dados e o número de telefone. O que devo fornecer?

O número de telefone e o endereço DC são fornecidos como parte da criação de emprego.

## <a name="miscellaneous"></a>Diversos

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>O que acontece se eu acidentalmente enviar um HDD que não está em conformidade com os requisitos suportados?

O centro de dados Azure devolverá a unidade que não está em conformidade com os requisitos suportados. Se apenas algumas das unidades do pacote cumprirem os requisitos de suporte, essas unidades serão processadas e as unidades que não satisfaçam os requisitos serão devolvidas a si.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>O serviço format as unidades antes de as devolver?

Não. Todas as unidades estão encriptadas com o BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Como posso aceder a dados importados por este serviço?

Utilize o portal Azure ou [o Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) para aceder aos dados na sua conta de armazenamento Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Depois da importação estar completa, como são os meus dados na conta de armazenamento? A minha hierarquia de diretório está preservada?

Ao preparar um disco rígido para um trabalho de importação, o destino é especificado pelo campo DstBlobPathOrPrefix no conjunto de dados CSV. Este é o recipiente de destino na conta de armazenamento para a qual os dados do disco rígido são copiados. Dentro deste recipiente de destino, são criados diretórios virtuais para pastas a partir do disco rígido e bolhas são criadas para ficheiros.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Se uma unidade tem ficheiros que já existem na minha conta de armazenamento, o serviço substitui as bolhas ou ficheiros existentes?

Depende. Ao preparar a unidade, pode especificar se os ficheiros de destino devem ser substituídos ou ignorados utilizando o campo no ficheiro CSV do conjunto de dados chamado Disposição:<renomear> sem sobrepor[sem sobrepor[sem sobrepor[sem sobrepor[sem sobrepor[sem sobrepor[sem sobrepor[sem sobrepor[sem sobrepor[sem sobrepor[sem sobreporosa.] Por predefinição, o serviço renomea os novos ficheiros em vez de substituir as bolhas ou ficheiros existentes.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>A ferramenta WAImportExport é compatível com sistemas operativos de 32 bits?

Não. A ferramenta WAImportExport é compatível apenas com sistemas operativos Windows de 64 bits. Para obter uma lista completa de Sistemas Operativos Suportados, vá a [Sistemas Operativos Suportados](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Qual é o tamanho máximo do Bloco Blob e da Página Blob suportado pela Importação/Exportação do Azure?

- O tamanho do Max Block Blob é de aproximadamente 4.768TB ou 5.000.000 MB.
- O tamanho do Max Page Blob é de 8TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>A Azure Import/Export supporta a encriptação AES-256?

Não. O serviço Azure Import/Export utiliza encriptação AES-128 BitLocker.

## <a name="next-steps"></a>Passos seguintes

* [O que é o serviço Importar/Exportar do Microsoft Azure?](storage-import-export-service.md)
