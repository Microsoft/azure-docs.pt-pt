---
title: FAQ do Microsoft Azure Data Box | Microsoft Docs em dados
description: Contém perguntas freqüentes e respostas para o Azure Data Box, uma solução de cloud que permite-lhe transferir grandes quantidades de dados para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 01/25/2019
ms.author: alkohli
ms.openlocfilehash: 9e4308eea3384b20f21ffcf378f711a705f9f9e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077348"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Perguntas Mais Frequentes

A solução híbrida Microsoft Azure Data Box permite-lhe enviar terabytes de dados para o Azure de forma rápida, económica e fiável através de um dispositivo de transferência. Estas FAQ contêm perguntas e respostas para dúvidas que possa ter ao utilizar o Azure Data Box no portal do Azure. 

As perguntas e respostas estão organizadas pelas seguintes categorias:

- Acerca do serviço
- Encomendar dispositivo
- Configurar e ligar 
- Acompanhar o estado
- Copiar dados 
- Enviar dispositivo
- Verificar e carregar dados 
- Suporte da cadeia de custódia

## <a name="about-the-service"></a>Acerca do serviço

### <a name="q-what-is-azure-data-box-service"></a>P. O que é o serviço Azure Data Box? 
R.  O serviço Azure Data Box foi concebido para ingestão de dados offline. Este serviço gere uma panóplia de produtos de diferentes capacidades de armazenamento, feitos à medida para o transporte de dados. 

### <a name="q-what-is-azure-data-box"></a>P. O que é o Azure Data Box?
R. O Azure Data Box permite a transferência rápida, económica e segura de terabytes de dados de e para o Azure. Pode encomendar o dispositivo Data Box no portal do Azure. Microsoft é fornecido um dispositivo de armazenamento de capacidade utilizável de 80 TB por meio de uma operadora regional. 

Quando receber o dispositivo, a configuração do mesmo na IU da Web local é rápida. Copie os dados dos seus servidores para o dispositivo e envie-o para o Azure. No datacenter do Azure, os seus dados são carregados automaticamente do dispositivo para o Azure. O serviço Data Box faz o acompanhamento do processo do início ao fim no portal do Azure.

### <a name="q-when-should-i-use-data-box"></a>P. Quando devo utilizar o Data Box?
R. Se tiver entre 40 a 500 TB de dados e quiser transferi-los para o Azure, pode tirar partido do Data Box. Para tamanhos de dados inferiores a 40 TB, utilize o Data Box Disk. Para tamanhos de dados superiores a 500 TB, inscreva-se no Data Box Heavy.

### <a name="q-what-is-the-price-of-data-box"></a>P. Qual é o preço do Data Box?
R. O Data Box está disponível a um custo nominal durante dez dias. Quando seleciona o modelo de produto durante a criação de uma encomenda no portal do Azure, são apresentados os custos do dispositivo. O envio também é gratuito; contudo, serão aplicados custos do armazenamento do Azure. Para obter mais informações, aceda aos [preços do Azure Data Box](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>P. Qual é a quantidade máxima de dados que posso transferir com o Data Box numa instância?
R. O Data Box tem uma capacidade bruta de 100 TB e uma capacidade utilizável de 80 TB. Pode transferir até 80 TB de dados com o Data Box. Para transferir mais dados, tem de encomendar mais dispositivos.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>P. Como posso saber se o Data Box está disponível na minha região? 
R.  Para obter informações relativamente aos países nos quais o Data Box está disponível, aceda a [Region availability](data-box-overview.md#region-availability) (Disponibilidade por região).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>P. Em que regiões posso armazenar dados com o Data Box?
R. O Data Box é suportado em todas as regiões dos E.U.A., da Europa Ocidental, da Europa do Norte, de França e do Reino Unido. Só são suportadas as regiões de cloud pública do Azure. O Azure Government e as outras clouds soberanas não são suportadas. Para obter mais informações, aceda a [Disponibilidade de região](data-box-overview.md#region-availability).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>P. Quem devo contactar se me deparar com problemas com o Data Box?
R. Se se deparar com problemas com o Data Box, contacte o [Suporte da Microsoft](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>P. Posso ter perdido meu Data Box. Existe um custo de dispositivo perdido?
R. Sim. Existe uma cobrança de dispositivo perdido ou danificado. Esta cobrança é abordada o [página de preços](https://azure.microsoft.com/pricing/details/storage/databox/) e na [produto termos de serviço](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Encomendar dispositivo

### <a name="q-how-do-i-get-data-box"></a>P. Como recebo o Data Box? 
R.  Para obter o Azure Data Box, inicie sessão no portal do Azure e crie uma encomenda de um dispositivo Data Box. Indique as suas informações de contacto e os seus detalhes de notificação. Depois de fazer a encomenda, o Data Box é-lhe enviado num prazo de dez dias, com base na disponibilidade. Para obter mais informações, aceda a [Order a Data Box](data-box-deploy-ordered.md) (Encomendar um Data Box).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>P. Não consegui criar uma encomenda de um dispositivo Data Box no portal do Azure. O que correu mal?
R. Se não conseguiu criar uma encomenda de um dispositivo Data Box, poderá haver um problema com o tipo ou o acesso da sua subscrição. 

Verifique a sua subscrição. Caixa de dados só está disponível para contrato Enterprise (EA) e na Cloud ofertas de subscrição de fornecedor (CSP) de solução. Se a sua subscrição não pertencer a nenhum destes tipos, contacte o Suporte da Microsoft para a atualizar.

Se o tipo da sua oferta de subscrição for suportado, verifique o nível de acesso da mesma. Para poder criar encomendas, tem de ser contribuidor ou proprietário na subscrição.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>P. Encomendei alguns dispositivos Data Box. Não consigo criar mais encomendas. O que correu mal?
R. Permitimos para um máximo de cinco pedidos ativos por subscrição por limites de comércio (combinação de país e a região selecionada). Se precisar de encomendar um dispositivo adicional, contacte o Suporte da Microsoft para aumentar o limite na sua subscrição.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>P. Quando tento criar uma encomenda, recebo uma notificação a indicar que o serviço Data Box não está disponível. O que é que isto quer dizer?
R. Isto quer dizer que o serviço Data Box não está disponível na combinação de país e região que selecionou. Alterar essa combinação irá, provavelmente, permitir-lhe utilizar o serviço. Para obter uma lista das regiões nas quais o serviço está disponível, aceda a [Region availability for Data Box](data-box-overview.md#region-availability) (Disponibilidade geográfica do Data Box).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>P. Fiz a minha encomenda do Data Box há alguns dias. Quando é que o vou receber?
R. Quando faz uma encomenda, nós verificamos se há algum dispositivo disponível para a mesma. Se sim, enviamos o mesmo dentro de dez dias. É possível que haja períodos de muita procura. Nesse caso, a encomenda é colocada em fila de espera e pode acompanhar a alteração do estado no portal do Azure. Se a encomenda não for satisfeita em 90 dias, é cancelada automaticamente.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>P. Já enchi o meu dispositivo Data Box com dados e preciso de encomendar outro. Existe alguma forma de fazer a encomenda rapidamente?
A. Pode clonar a encomenda anterior. A clonagem cria a mesma encomenda e permite-lhe editar os detalhes da mesma, sem ter de voltar a introduzir as informações de morada, contacto e notificação.

## <a name="configure-and-connect"></a>Configurar e ligar

### <a name="q-how-do-i-unlock-the-data-box"></a>P. Como posso desbloquear o Data Box? 
R.  No portal do Azure, aceda à encomenda do Data Box e navegue para **Detalhes do dispositivo**. Copie a palavra-passe de bloqueio. Utilize-a para iniciar sessão na IU da Web no seu Data Box. Para obter mais informações, aceda a [Tutorial: Descompactar, ligar e ligar o Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>P. Posso utilizar um computador anfitrião Linux para ligar e copiar os dados para os discos do Data Box?
R.  Sim. Pode utilizar o Data Box para ligar a clientes SMB e NFS. Para obter mais informações, aceda à lista de [Sistemas operativos suportados](data-box-system-requirements.md) do seu computador anfitrião.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>P. O meu Data Box foi enviado, mas quero cancelar esta encomenda. Por que é que o botão de cancelamento não está disponível?
R.  Só pode cancelar a encomenda depois de o Data Box ter sido encomendado e antes de a encomenda ser processada. Assim que a encomenda for processada, já não a pode cancelar. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>P. Posso ligar um dispositivo Data Box a vários computadores anfitriões para transferir dados?
R. Sim. É possível ligar vários computadores anfitriões ao Data Box para transferir dados e podem ser executados múltiplos trabalhos de cópia em paralelo. Para obter mais informações, aceda a [Tutorial: Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>P. Posso ligar a ambas as interfaces de 10 GbE no Data Box para transferir dados?
R. Sim. Ambas as interfaces de 10 GbE podem ser ligadas na caixa de dados para copiar dados em simultâneo. Para obter mais informações sobre como copiar dados, aceda a [Tutorial: Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>P. O LED indicador de falha do sistema no painel de funcionamento frontal está ligado. O que devo fazer?
R. Se o LED indicador de falha do sistema estiver ligado, significa que o sistema não está em bom estado de funcionamento. [Contacte o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md) para saber quais os próximos passos.

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>P. Não consigo aceder à palavra-passe de desbloqueio do Data Box no portal do Azure. O que correu mal?
R. Se não conseguir aceder à palavra-passe de desbloqueio no portal do Azure, verifique as permissões na sua subscrição e na sua conta de armazenamento. Confirme que tem a permissão Contribuidor ou Proprietário ao nível do grupo de recursos. Se não, em seguida, tem de ter, pelo menos, permissão de função de operador de caixa de dados para ver as credenciais de acesso.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>P. Configuração do canal de porta é suportada na caixa de dados? E quanto a MPIO?
R. Não suportamos a configuração do canal de porta, a configuração de Multipath e/s (MPIO) ou a configuração de vLAN no Data Box.

## <a name="track-status"></a>Acompanhar o estado

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>P. Como posso fazer o acompanhamento do Data Box desde o momento em que fiz a encomenda até à devolução do dispositivo? 
R.  Pode acompanhar o estado da encomenda do Data Box no portal do Azure. Quando cria a encomenda, também lhe é pedido que indique um e-mail para notificações. Se o tiver indicado, todas as alterações ao estado da encomenda são-lhe notificadas por e-mail. Mais informações sobre como [configurar os e-mails de notificações](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>P. Como posso devolver o dispositivo? 
R.  A Microsoft mostra uma guia de remessa no ecrã E-Ink. Se a guia de remessa não aparecer no ecrã E-Ink, aceda a **Descrição Geral > Transferir guia de remessa**. Transfira e imprima a guia, insira-a na capa plástica transparente no dispositivo e entregue o dispositivo à transportadora. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>P. Recebi uma notificação por e-mail a dizer que o meu dispositivo chegou ao datacenter do Azure. Como posso saber se o carregamento dos dados está a decorrer?
R. Pode ver a sua encomenda do Data Box no portal do Azure e aceder a **Descrição Geral**. Se o carregamento dos dados para o Azure tiver começado, verá o progresso da cópia no painel do lado direito. 

## <a name="migrate-data"></a>Migrar dados

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>P. Qual é o tamanho máximo de dados que pode ser utilizado com o Data Box?  
R.  O Data Box tem uma capacidade de armazenamento utilizável de 80 TB. Pode utilizar um dispositivo Data Box individual para dados que variem entre os 40 TB e os 80 TB. Para os tamanhos até 500 TB de dados maiores, é possível pedir vários dispositivos de Data Box. Para tamanhos que excedam os 500 TB, inscreva-se no Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>P. Quais são os tamanhos máximos de blobs de blocos e de blobs de páginas que o Data Box suporta? 
R.  Os tamanhos máximos são regidos pelos limites do Armazenamento do Azure. O tamanho máximo dos blobs de blocos é de, aproximadamente, 4,768 TiB e o dos blobs de páginas é de 8 TiB. Para obter mais informações, aceda a [Azure Storage Scalability and Performance Targets](../storage/common/storage-scalability-targets.md) (Metas de Desempenho e Escalabilidade do Armazenamento do Azure). 

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>P. Como posso saber que os meus dados estão seguros durante o transporte? 
R. Foram implementadas múltiplas funcionalidades de segurança para garantir que o seu Data Box está seguro durante o transporte. Algumas dessas funcionalidades incluem selos invioláveis, deteção de adulteração de hardware e software e palavra-passe de desbloqueio do dispositivo. Para obter mais informações, aceda a [Azure Data Box security and data protection](data-box-security.md) (Segurança e proteção de dados do Azure Data Box).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>P. Como posso copiar os dados para o Data Box? 
R.  Se estiver a utilizar um cliente SMB, pode utilizar uma ferramenta de cópia de SMB, como o Robocopy, o Diskboss ou até mesmo o arrastar e largar do Explorador de Ficheiros do Windows, para copiar os dados para o dispositivo. 

Se estiver a utilizar um cliente NFS, pode utilizar [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) ou [Ultracopier](https://ultracopier.first-world.info/). 

Para obter mais informações, aceda a [Tutorial: Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>P. Estão disponíveis sugestões para acelerar a cópia dos dados?
A.  Para acelerar o processo de cópia:

- Utilize vários fluxos de cópia de dados. Por exemplo, com o Robocopy, utilize a opção multithreaded. Para obter mais informações sobre o comando exato utilizado, aceda a [Tutorial: Copiar dados para o Azure Data Box e certifique-se](data-box-deploy-copy-data.md).
- Utilize várias sessões.
- Em vez de copiar através de uma partilha de rede (em que pode estar limitado pelas velocidades da rede), certifique-se de que os dados residem localmente no computador ao qual o Data Box vai ser ligado.
- Compare o desempenho do computador utilizado para copiar os dados. Transfira e utilize a [ferramenta Bluestop FIO](https://bluestop.org/fio/) para comparar o desempenho do hardware do servidor.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>P. Posso utilizar várias contas de armazenamento com o Data Box?
R.  Sim. O Data Box suporta um máximo de dez contas de armazenamento, sejam de fins gerais, clássicas ou de blobs. São suportados tanto os blobs de acesso frequente, como os de acesso esporádico. Durante o lançamento GA, são suportadas as contas de armazenamento em todas as regiões dos EUA, da Europa Ocidental, da Europa do Norte, de França e do Reino Unido na cloud pública do Azure.


## <a name="ship-device"></a>Enviar dispositivo

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>P. O meu dispositivo foi entregue, mas parece estar danificado. O que devo fazer?
R. Se o dispositivo foi entregue danificado ou se houver provas de adulteração, não o utilize. [Contacte o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md) e devolva o dispositivo o mais depressa possível. Também pode criar uma encomenda do Data Box nova para substituir o dispositivo. Nesse caso, o dispositivo de substituição não lhe é cobrado.

### <a name="q-can-i-use-my-own-shipping-carrier-to-ship-data-box"></a>P. Posso utilizar a minha própria transportadora para enviar o Data Box?
R. Para o serviço Data Box, a Microsoft processa o envio de e para o datacenter do Azure. Se quiser utilizar a sua própria transportadora, pode utilizar o serviço Importar/Exportar do Azure. Para obter mais informações, aceda a [What is Azure Import/Export service?](../storage/common/storage-import-export-service.md) (O que é o serviço Importar/Exportar do Azure?).

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>P. O ecrã E-Ink não está a mostrar a guia de remessa da devolução. O que devo fazer?
R. Se o ecrã E-Ink não mostrar a guia de remessa da devolução, siga os passos abaixo:
- Retire a guia de remessa antiga e todos os autocolantes do envio anterior.
- Aceda à encomenda no portal do Azure. Aceda a **Descrição Geral** e **Transfira a guia de remessa**. Para obter mais informações, aceda a [Download shipping label](data-box-portal-admin.md#download-shipping-label) (Transferir a guia de remessa).
- Imprima a guia e insira-a no invólucro plástico transparente que está colado ao dispositivo. 
- Confirme que a guia está claramente visível. 

### <a name="q-how-is-my-data-protected-during-transit"></a>P. Como é que os meus dados são protegidos durante o transporte? 
R.  Durante o transporte, os dados são protegidos pelas seguintes funcionalidades do Data Box.
 - Os discos do Data Box são encriptados com encriptação AES de 256 bits. 
 - O dispositivo é bloqueado e exige que seja introduzida uma palavra-passe de desbloqueio para aceder aos dados.
Para obter mais informações, aceda a [Data Box security features](data-box-security.md) (Funcionalidades de segurança do Data Box).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>P. Concluí a preparação para envio e encerrei o dispositivo. Ainda posso adicionar mais dados ao Data Box?
R. Sim. Pode ligar o dispositivo e adicionar mais dados. Quando concluir a cópia dos dados, tem de **Preparar o Envio** outra vez.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>P. Recebi o meu dispositivo e não esteja conseguindo cópia de segurança? Como posso enviar o dispositivo novamente?
R. Se o seu dispositivo não esteja conseguindo, vá para a sua encomenda no portal do Azure. Transfira uma etiqueta de envio e affix que no dispositivo. Para obter mais informações, aceda a [Download shipping label](data-box-portal-admin.md#download-shipping-label) (Transferir a guia de remessa).

## <a name="verify-and-upload"></a>Verificar e carregar

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>P. Quanto tempo depois de devolver o Data Box é que posso aceder aos meus dados no Azure? 
R.  Quando o estado da encomenda para **Cópia de Dados** aparecer como **Concluído**, deverá conseguir aceder aos seus dados de imediato.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>P. Depois do carregamento, onde é que os meus dados ficam localizados no Azure?
R.  Quando copiar os dados para o Data Box, dependendo se os dados são o blob de blocos ou BLOBs de páginas ou os ficheiros do Azure, os dados são carregados para um dos seguintes caminhos na sua conta de armazenamento do Azure.
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>P. Acabei de reparar que não segui os requisitos de nomenclatura do Azure nos meus contentores. O carregamento dos meus dados para o Azure vai falhar?
R.  Se os nomes de contentor têm Letra maiúscula, em seguida, esses nomes são automaticamente convertidos em minúsculas. Se os nomes não estiverem em conformidade por outros motivos (carateres especiais, outros idiomas, etc.), o carregamento falhará. Para obter mais informações sobre as melhores práticas de nomenclatura de partilhas, contentores e ficheiros, aceda a:
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Nomenclatura e referência a partilhas)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Convenções de blobs de blocos e de blobs de páginas).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>P. Como posso verificar os dados que copiei para o Data Box?
R.  Após a conclusão da cópia dos dados, quando executa **Preparação para envio**, os seus dados são validados. Durante o processo de validação, o Data Box gera uma lista de ficheiros e somas de verificação para os dados. Pode transferir a lista de ficheiros e certifique-se a lista de ficheiros nos dados de origem. Para obter mais informações, aceda a [Prepare to ship](data-box-deploy-picked-up.md#prepare-to-ship) (Preparar para enviar).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>P. O que acontece aos dados depois de devolver o Data Box?
R.  Após a conclusão da cópia dos dados para o Azure, os dados dos discos no Data Box são apagados em segurança, de acordo com as diretrizes SP 800-88 Revision 1 da NIST. Para obter mais informações, aceda a [Erasure of data from Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box) (Apagar dados do Data Box).

## <a name="audit-report"></a>Relatório de auditoria

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Como é que o serviço Azure Data Box ajuda a suportar o procedimento da cadeia de custódia dos clientes?
R.  O serviço Azure Data Box disponibiliza, de forma nativa, relatórios que podem ser utilizados para a documentação da cadeia de custódia. Os registos de auditorias e de cópias estão disponíveis na sua conta de armazenamento no Azure e pode [transferir o histórico de encomendas](data-box-portal-admin.md#download-order-history) no portal do Azure, após o mesmo estar concluído.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Que tipos de relatórios estão disponíveis para suportar a cadeia de custódia?
R.  Para suportar a cadeia de custódia, estão disponíveis os seguintes relatórios:

- Logística de UPS de transporte.
- Registo de ligar e de partilha de acesso do utilizador.
- Ficheiro de manifesto com uma verificação de redundância cíclica de 64 bits (CRC-64) ou soma de verificação para cada ficheiro que é ingerido pelo Data Box.
- Relatórios de ficheiros que não foram devidamente carregados para a conta de armazenamento do Azure.
- Limpeza do dispositivo Data Box (de acordo com as normas 800 88R1 da NIST) após a cópia dos dados para a sua conta de armazenamento do Azure.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Está a operadora controlo registos (a partir de UPS) disponível? 
R.  Os registos de acompanhamento da transportadora são guardados no histórico de encomendas do Data Box. Este relatório está disponível após o dispositivo ter chegado ao datacenter do Azure e de os dados no mesmo terem sido limpos. Para necessidades imediatas, também pode aceder diretamente ao site da transportadora com o número de acompanhamento da encomenda e obter as informações de acompanhamento.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Posso transportar o Data Box para o datacenter do Azure? 
R.  Não. Atualmente o datacenter do Azure não aceita a entrega da caixa de dados de clientes ou de operadoras diferente no-BREAK.


## <a name="next-steps"></a>Passos Seguintes

- Reveja os [requisitos de sistema do Data Box](data-box-system-requirements.md).
- Compreenda os [limites do Data Box](data-box-limits.md).
- Implemente rapidamente o [Azure Data Box](data-box-quickstart-portal.md) no portal do Azure.
