---
title: FAQ para serviço de importação/exportação Azure / Microsoft Docs
description: Leia respostas a perguntas frequentes sobre o serviço de exportação de importações Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 08c105bd8ae9b5e0667ef5279e6c9484c631bd48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399045"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Serviço de Importação/Exportação Azure: perguntas frequentes

Seguem-se perguntas e respostas que poderá ter quando utilizar o seu serviço Azure Import/Export para transferir dados para o armazenamento da Azure. As perguntas e respostas estão organizadas pelas seguintes categorias:

- Sobre o serviço de importação/exportação
- Preparação dos discos para importação/exportação
- Postos de trabalho nas importações/exportações
- Envio de discos
- Diversos

## <a name="about-importexport-service"></a>Sobre o serviço de importação/exportação

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Posso copiar o armazenamento de ficheiros Azure utilizando o serviço Azure Import/Export?

Sim. O serviço Azure Import/Export suporta a importação para o armazenamento de ficheiros Azure. Não suporta a exportação de Ficheiros Azure neste momento.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>O serviço Azure Import/Export está disponível para assinaturas CSP?

Sim. O serviço Azure Import/Export suporta subscrições de Fornecedores de Soluções Cloud (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-microsoft-365"></a>Posso utilizar o serviço Azure Import/Export para copiar caixas de correio PST e dados do SharePoint para o Microsoft 365?

Sim. Para mais informações, aceda ao [resumo da importação dos ficheiros PST da sua organização.](/microsoft-365/compliance/importing-pst-files-to-office-365)

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Posso utilizar o serviço Azure Import/Export para copiar as minhas cópias de segurança offline para o Serviço de Backup Azure?

Sim. Para obter mais informações, aceda ao [fluxo de trabalho de backup offline em Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Posso comprar unidades para empregos de importação/exportação da Microsoft?

N.º Tens de enviar os teus próprios discos para trabalhos de importação e exportação.

## <a name="preparing-disks-for-importexport"></a>Preparação de discos para importação/exportação

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Posso saltar o passo de preparação para um trabalho de importação? Posso preparar uma unidade sem copiar?

N.º Qualquer unidade utilizada para importar dados deve ser preparada utilizando a ferramenta Azure WAImportExport. Utilize a ferramenta para também copiar dados para a unidade.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Preciso de fazer alguma preparação em disco quando criar um trabalho de exportação?

N.º Recomenda-se alguns pré-exames. Para verificar o número de discos necessários, utilize o comando PreviewExport da ferramenta WAImport. Para obter mais informações, consulte [pré-visualização do uso da unidade para um trabalho de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). O comando ajuda-o a visualizar o uso da unidade para as bolhas selecionadas, com base no tamanho das unidades que vai utilizar. Verifique também se pode ler e escrever para o disco rígido que é enviado para o trabalho de exportação.

## <a name="importexport-jobs"></a>Postos de trabalho nas importações/exportações

### <a name="can-i-cancel-my-job"></a>Posso cancelar o meu emprego?

Sim. Pode cancelar um trabalho quando o seu estado é **Criar** ou **Enviar.** Para além destas fases, o trabalho não pode ser cancelado e continua até à fase final.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Quanto tempo posso ver o estado dos trabalhos concluídos no portal Azure?

Pode ver o estado dos trabalhos concluídos até 90 dias. Os trabalhos concluídos são eliminados após 90 dias.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?

Um trabalho de importação ou exportação só pode fazer referência a 10 unidades num único trabalho. Para enviar mais de 10 unidades, deve criar vários empregos. As unidades associadas ao mesmo trabalho devem ser enviadas juntas no mesmo pacote.
Para obter mais informações e orientação quando a capacidade de dados abranger vários trabalhos de importação de discos, contacte o Microsoft Support.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>A bolha carregada mostra o estado como "Arrendamento expirado". O que devo fazer?

Pode ignorar o campo "Arrendamento Expirado". A Importação/Exportação aluga a bolha durante o upload para se certificar de que nenhum outro processo pode atualizar a bolha em paralelo. O arrendamento expirado implica que a Importação/exportação já não está a ser carregada e a bolha está disponível para o seu uso.

## <a name="shipping-disks"></a>Envio de discos

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Qual é o número máximo de HDD num só carregamento?

Não há limite para o número de HDDs num único carregamento. Se os discos pertencerem a vários trabalhos, recomendamos que:

- rotular os discos com os respetivos nomes de emprego.
- atualizar os postos de trabalho com um número de rastreio sufixado com -1, -2 etc.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Devo incluir algo além do HDD no meu pacote?

Envie apenas os seus discos rígidos no pacote de envio. Não inclua itens como cabos de alimentação ou cabos USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Tenho de enviar os meus discos usando a FedEx ou a DHL?

Pode enviar unidades para o datacenter Azure usando qualquer transportadora conhecida como FedEx, DHL, UPS ou Us Postal Service. No entanto, para envio de unidades de devolução a partir do datacenter, deve fornecer:

- Um número de conta FedEx nos EUA e UE, ou
- Um número de conta DHL nas regiões da Ásia e Austrália.

> [!NOTE]
> Os centros de dados na Índia exigem uma carta de declaração no seu papel timbrado (challan de entrega) para devolver as unidades. Para organizar o passe de entrada necessário, também deve reservar a recolha com a sua transportadora selecionada e partilhar os detalhes com o datacenter.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Existem restrições com o envio e devolvendo a minha unidade internacionalmente?

Por favor, note que os meios físicos que está a enviar podem precisar de atravessar fronteiras internacionais. É responsável por garantir que os seus meios físicos e dados são importados e/ou exportados de acordo com as leis aplicáveis. Antes de enviar os meios físicos, consulte os seus conselheiros para verificar se os seus meios de comunicação e dados podem ser legalmente enviados para o centro de dados identificado. Isto ajudará a garantir que chega à Microsoft em tempo útil.

Após o carregamento estar concluído, o processo de devolução de unidades a um endereço internacional pode demorar mais do que os 2-3 dias típicos necessários para o envio local. Durante a fase enumerada no portal Azure como Embalagem, a equipa da Caixa de Dados está a garantir que a documentação correta é fornecida para garantir que a expedição está em conformidade com os vários requisitos internacionais de importação e exportação.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Existem requisitos especiais para entregar os meus discos a um datacenter?

Os requisitos dependem das restrições específicas do datacenter Azure.

- Existem alguns sites, como a Austrália, Alemanha e Reino Unido Sul, que exigem que um número de ID de entrada de dados da Microsoft seja escrito na encomenda por razões de segurança. Antes de enviar as suas unidades ou discos para o datacenter, contacte a Azure DataBox Operations ( adbops@microsoft.com ) para obter este número. Sem este número, o pacote será rejeitado.
- Os centros de dados na Índia requerem os detalhes pessoais do condutor, como o Cartão de Identificação do Governo ou o Nº de Prova. (por exemplo, PAN, AADHAR, DL), nome, contacto e o número da placa do carro para obter um passe de entrada do portão. Para evitar atrasos na entrega, informe a sua transportadora sobre estes requisitos.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Ao criar um emprego, o endereço de envio é um local diferente da localização da minha conta de armazenamento. O que devo fazer?

Alguns locais de conta de armazenamento estão mapeados para locais de envio alternativos. Os locais de envio previamente disponíveis também podem ser temporariamente mapeados para locais alternativos. Verifique sempre o endereço de envio fornecido durante a criação de emprego antes de enviar as suas unidades.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Ao enviar a minha unidade, a transportadora pede o endereço de contacto do data center e o número de telefone. O que devo providenciar?

O número de telefone e o endereço dc são fornecidos como parte da criação de emprego.

## <a name="miscellaneous"></a>Diversos

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>O que acontece se eu enviar acidentalmente um HDD que não está em conformidade com os requisitos suportados?

O centro de dados Azure devolverá a unidade que não está em conformidade com os requisitos suportados. Se apenas algumas das unidades do pacote satisfarem os requisitos de suporte, essas unidades serão processadas e as unidades que não satisfaçam os requisitos serão devolvidas a si.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>O formato de serviço é o acionamento antes de os devolver?

N.º Todas as unidades estão encriptadas com o BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Como posso aceder aos dados que são importados por este serviço?

Utilize o portal Azure ou [o Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) para aceder aos dados na sua conta de armazenamento Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Depois de a importação estar completa, como são os meus dados na conta de armazenamento? A minha hierarquia de diretório está preservada?

Ao preparar um disco rígido para uma tarefa de importação, o destino é especificado pelo campo DstBlobPathOrPrefix no CSV do conjunto de dados. Este é o contentor de destino na conta de armazenamento para o qual os dados do disco rígido são copiados. Dentro deste contentor de destino, são criados diretórios virtuais para pastas a partir do disco rígido e são criadas bolhas para ficheiros.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Se uma unidade tiver ficheiros que já existem na minha conta de armazenamento, o serviço substitui as bolhas ou ficheiros existentes?

Depende. Ao preparar a unidade, pode especificar se os ficheiros de destino devem ser substituídos ou ignorados utilizando o campo no ficheiro CSV do conjunto de dados chamado Disposition:<renomeação /não-overwrite ]overwrite>. Por predefinição, o serviço renomea os novos ficheiros em vez de substituir as bolhas ou ficheiros existentes.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>A ferramenta WAImportExport é compatível com sistemas operativos de 32 bits?

N.º A ferramenta WAImportExport só é compatível com sistemas operativos Windows de 64 bits. Para obter uma lista completa de SISTEMAS Operativos Suportados, aceda a [Sistemas Operativos Suportados.](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements)

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Qual é o tamanho máximo de blob de bloco e de página suportado pela Azure Import/Export?

- O tamanho do Bloco Max Blob é aproximadamente 4.768TB ou 5.000.000 MB.
- O tamanho do Blob de Página Max é de 8TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>A Azure Import/Export suporta a encriptação AES-256?

Sim. O serviço Azure Import/Export utiliza encriptação AES-256 BitLocker.

## <a name="next-steps"></a>Passos seguintes

* [O que é o serviço Importar/Exportar do Microsoft Azure?](storage-import-export-service.md)
