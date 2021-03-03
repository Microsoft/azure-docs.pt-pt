---
title: FAQ do Microsoft Azure Data Box Disk | Microsoft Docs em dados
description: Contém perguntas e respostas frequentes para O Disco de Caixa de Dados Azure, uma solução em nuvem que permite transferir grandes quantidades de dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: 7212fc4113c1de0a7aee4c6c02e8fa65f9828680
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724834"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Disco de caixa de dados Azure: Perguntas frequentes

A solução na cloud Microsoft Azure Data Box Disk permite-lhe enviar terabytes de dados para o Azure de forma rápida, económica e fiável. Estas FAQ contêm perguntas e respostas para dúvidas que possa ter ao utilizar o Azure Data Box Disk no portal do Azure. 

As perguntas e respostas estão organizadas pelas seguintes categorias:

- Acerca do serviço
- Configurar e ligar 
- Acompanhar o estado
- Migrar dados 
- Verificar e carregar dados 


## <a name="about-the-service"></a>Acerca do serviço

### <a name="q-what-is-azure-data-box-service"></a>P. O que é o serviço Azure Data Box? 
A.  O serviço Azure Data Box foi concebido para ingestão de dados offline. Este serviço gere uma panóplia de produtos feitos à medida para o transporte de dados para diferentes capacidades de armazenamento. 

### <a name="q-what-are-azure-data-box-disks"></a>P. O que são os discos do Azure Data Box Disk?
A. Os discos do Azure Data Box Disk permitem a transferência rápida, económica e segura de terabytes de dados de e para o Azure. A Microsoft envia-lhe um a cinco discos, com uma capacidade de armazenamento máxima de 35 TB. Pode configurar, ligar e desbloquear facilmente estes discos através do serviço Data Box no portal do Azure.  

Os discos são encriptados com a Encriptação de Unidade BitLocker da Microsoft e as suas chaves de encriptação são geridas no portal do Azure. Em seguida, copia os dados dos servidores do cliente. No datacenter, a Microsoft migra os seus dados da unidade para a cloud através de uma ligação de carregamento de rede rápida e privada e carrega-os para o Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>P. Quando devo utilizar os discos do Data Box Disk?
A. Se tiver 40 TB (ou menos) de dados e quiser transferi-los para o Azure, pode tirar partido dos discos do Data Box Disk.

### <a name="q-what-is-the-price-of-data-box-disks"></a>P. Qual é o preço dos discos do Data Box Disk?
A. Para obter informações sobre o preço dos Discos caixa de dados, [aceda](https://azure.microsoft.com/pricing/details/databox/disk/)à página de Preços .

### <a name="q-how-do-i-get-data-box-disks"></a>P. Como recebo os discos do Data Box Disk? 
A.  Para obter discos de caixa de dados Azure, inicie sessão no portal Azure e crie uma encomenda de caixa de dados para discos. Indique as suas informações de contacto e os seus detalhes de notificação. Depois de fazer a encomenda, os discos são-lhe enviados num prazo de dez dias, com base na disponibilidade.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>P. Qual é a quantidade máxima de dados que posso transferir com os discos do Data Box Disk numa instância?
A. Para cinco discos, cada um com capacidade de 8-TB (7 TB de capacidade utilizável), a capacidade máxima utilizável é de 35 TB. Para que possa transferir 35 TB de dados num só caso. Para transferir mais dados, tem de encomendar mais discos.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>P. Como posso saber se os discos do Data Box Disk estão disponíveis na minha região? 
A.  Para ver onde os Discos de Caixa de Dados estão atualmente disponíveis, aceda à disponibilidade da [Região.](data-box-disk-overview.md#region-availability)  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>P. Em que regiões posso armazenar dados com os discos do Data Box Disk?
A. O Disco de Caixa de Dados é suportado para todas as regiões dos EUA, Canadá, Austrália, Europa Ocidental e Europa do Norte, Coreia e Japão. Só são suportadas as regiões de cloud pública do Azure. O Azure Government e as outras clouds soberanas não são suportadas.

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>P. Como posso importar dados de origem presentes na minha localização num país/região para uma região de Azure num país diferente?
A. O Data Box Disk suporta a ingestão de dados apenas no mesmo país/região que o seu destino e não atravessará nenhuma fronteira internacional. A única exceção é para encomendas na União Europeia (UE), onde os discos de caixa de dados podem enviar de e para qualquer país/região da UE.

Por exemplo, se quiser transferir dados na sua localização no Canadá para uma conta de armazenamento Azure West US, então poderá alcançá-lo da seguinte forma:

#### <a name="option-1"></a>Opção 1: 

Envie um [disco suportado](../import-export/storage-import-export-requirements.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#supported-disks) contendo dados utilizando o [serviço Azure Import/Export](../import-export/storage-import-export-service.md) da localização de origem no Canadá para o centro de dados Azure West US.

#### <a name="option-2"></a>Opção 2:

1. Encomende o Disco de Caixa de Dados no Canadá, escolhendo uma conta de armazenamento no Canadá Central. Os discos SSD são enviados do centro de dados Azure no Canadá Central para o endereço de envio (no Canadá) fornecido durante a criação da encomenda.

2. Depois de os dados do servidor no local terem sido copiados para os discos, devolva-os ao datacenter Azure no Canadá utilizando etiquetas de devolução fornecidas pela Microsoft. Os dados presentes no(s) Data Box Disk(s) são depois enviados para a conta de armazenamento de destino na região do Canadá Azure escolhida durante a criação da encomenda.

3. Em seguida, pode utilizar uma ferramenta como a AzCopy para copiar os dados para uma conta de armazenamento nos EUA. Este passo incorre em [cargas de armazenamento padrão](https://azure.microsoft.com/pricing/details/storage/) e [largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) que não estão incluídas na faturação do disco de caixa de dados.

#### <a name="q-does-data-box-disk-store-any-customer-data-outside-of-the-service-region"></a>P. O Disco de Caixa de Dados armazena quaisquer dados do cliente fora da região de serviço?

A. N.º O Disco de Caixa de Dados não armazena quaisquer dados do cliente fora da região de serviço. O cliente tem a propriedade total dos seus dados e pode guardar os dados para um local especificado com base na conta de armazenamento que selecionam durante a criação da encomenda.  

Além dos dados do cliente, existem dados de Disco de Caixa de Dados que incluem metadados e registos de monitorização. Em todas as regiões (exceto Brasil Sul e Sudeste Asiático), os dados do Disco de Caixa de Dados são armazenados e replicados numa [região emparelhada](../best-practices-availability-paired-regions.md) através de uma conta de Armazenamento Geo-redundante para proteger contra a perda de dados.  

Devido aos [requisitos de residência](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) de dados no Brasil Sul e Sudeste Asiático, os dados do Disco de Caixa de Dados são armazenados numa conta de armazenamento redundante (ZRS) para que seja contido numa única região. Para o Sudeste Asiático, todos os dados do Disco de Caixa de Dados são armazenados em Singapura e para o Brasil Sul, os dados são armazenados no Brasil. 

Se houver uma paragem de serviço no Brasil Sul e Sudeste Asiático, os clientes podem criar novas encomendas a partir de outra região. As novas encomendas serão servidas a partir da região em que são criadas e os clientes são responsáveis pelo envio de e para o fro do Disco caixa de dados.



### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>P. Como posso recuperar os meus dados se uma região inteira falha?

A. Em circunstâncias extremas em que uma região se perde devido a um desastre significativo, a Microsoft pode iniciar um fracasso regional. Não é necessária nenhuma ação da sua parte neste caso. A sua ordem será cumprida através da região de failover se estiver dentro do mesmo país ou fronteira comercial. No entanto, algumas regiões de Azure não têm uma região emparelhada na mesma fronteira geográfica ou comercial. Se houver uma catástrofe em qualquer uma dessas regiões, terá de criar novamente a encomenda da Caixa de Dados a partir de uma região diferente que está disponível, e copiar os dados para o Azure na nova região. Para obter mais informações, veja [Continuidade de negócio e recuperação após desastre (BCDR): Regiões Emparelhadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>P. Quem devo contactar se me deparar com problemas com os discos do Data Box Disk?
A. Se encontrar algum problema com discos de caixa de dados, [contacte o Microsoft Support](./data-box-disk-contact-microsoft-support.md).

## <a name="order-device"></a>Encomendar dispositivo

### <a name="q-how-do-i-get-data-box-disk"></a>P. Como consigo o disco da caixa de dados? 
A.  Para obter o Disco caixa de dados Azure, inscreva-se no portal Azure e crie uma ordem de disco de caixa de dados. Indique as suas informações de contacto e os seus detalhes de notificação. Uma vez que esemiste um pedido, com base na disponibilidade, o Disco de Caixa de Dados é enviado para si dentro de 10 dias. Para obter mais informações, aceda a [Order a Data Box](data-box-disk-deploy-ordered.md) (Encomendar um Data Box).

### <a name="q-i-couldnt-create-a-data-box-disk-order-in-the-azure-portal-why"></a>P. Não consegui criar uma ordem de disco de caixa de dados no portal Azure. Porquê?
A. Se não conseguir criar uma encomenda de disco de caixa de dados, existe um problema com o seu tipo de subscrição ou acesso.

Verifique a sua subscrição. O Disco de Caixa de Dados só está disponível para ofertas de subscrição do Enterprise Agreement (EA) e do Fornecedor de Soluções Cloud (CSP). Se não tiver nenhum destes tipos de subscrição, contacte o Microsoft Support para atualizar a sua subscrição.

Se o tipo da sua oferta de subscrição for suportado, verifique o nível de acesso da mesma. Para poder criar encomendas, tem de ser contribuidor ou proprietário na subscrição.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>P. Quanto tempo demorará a minha encomenda desde a criação de encomendas até aos dados enviados para o Azure?

A. Os seguintes prazos de entrega estimados para cada fase de processamento da encomenda dar-lhe-ão uma boa ideia do que esperar.  

Estes prazos são *estimativas.* O tempo para cada fase de processamento da encomenda é afetado pela carga no datacenter, encomendas simultâneas e outras condições ambientais.

**Prazos de entrega estimados para uma encomenda de disco de caixa de dados:**

1. Disco de caixa de dados de encomenda: A poucos minutos do portal
2. Atribuição e preparação do disco: Até 5 dias úteis, dependendo da disponibilidade do inventário e do número de encomendas pendentes a serem processadas
3. Envio: 2 a 3 dias úteis
4. Cópia de dados no site do cliente: Depende da natureza dos dados, tamanho e número de ficheiros.
5. Devolução: 2 a 3 dias úteis
6. O processamento no datacenter e o upload para Azure: O upload de dados começa no datacenter assim que o processamento operacional estiver concluído e o disco estiver ligado. O tempo de upload depende da natureza dos dados, tamanho e número de ficheiros.

## <a name="configure-and-connect"></a>Configurar e ligar
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>P. Posso especificar o número de discos do Data Box Disk na encomenda?
A.  N.º Obtém discos de 8-TB (um máximo de cinco discos) dependendo do tamanho dos seus dados e disponibilidade dos discos.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>P. Como posso desbloquear os discos do Data Box Disk? 
A.  No portal do Azure, aceda à encomenda do Data Box Disk e navegue para **Detalhes do dispositivo**. Copie a chave de acesso. Transfira e instale a ferramenta de desbloqueio do Data Box Disk do portal do Azure para o seu sistema operativo. Execute a ferramenta no computador que tem os dados que pretende copiar para os discos. Indique a chave de acesso para desbloquear os discos. A mesma chave de acesso desbloqueia todos os discos. 

Para obter instruções passo a passo, aceda a [Desbloquear discos num cliente Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) ou [Desbloquear discos num cliente Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>P. Posso utilizar um computador anfitrião Linux para ligar e copiar os dados para os discos do Data Box Disk?
A.  Sim. Os clientes Linux e Windows podem ser utilizados para ligar e copiar os dados para os discos do Data Box. Para obter mais informações, aceda à lista de [Sistemas operativos suportados](data-box-disk-system-requirements.md) do seu computador anfitrião.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>P. Os meus discos foram enviados, mas quero cancelar esta encomenda. Por que é que o botão de cancelamento não está disponível?
A.  Só pode cancelar a encomenda depois de os discos terem sido encomendados e antes de serem enviados. Após o envio, já não pode cancelar a encomenda. No entanto, pode devolver os seus discos com uma carga. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>P. Posso ligar vários discos do Data Box Disk ao mesmo computador anfitrião para transferir os dados?
A. Sim. É possível ligar vários discos do Data Box ao mesmo computador anfitrião para transferir os dados e podem ser executados múltiplos trabalhos de cópia em paralelo.

## <a name="track-status"></a>Acompanhar o estado

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>P. Como posso fazer o acompanhamento desde o momento em que fiz a encomenda até à devolução dos discos? 
A.  Pode acompanhar o estado da encomenda do Data Box Disk no portal do Azure. Quando cria a encomenda, também lhe é pedido que indique um e-mail para notificações. Se forneceu uma, então é notificado por e-mail sobre todas as alterações de estado da encomenda. Mais informações sobre como [configurar os e-mails de notificações](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>P. Como posso devolver os discos? 
A.  A Microsoft fornece uma etiqueta de envio com o Data Box Disk na embalagem. Afixe a etiqueta à caixa de envio e entregue a embalagem fechada na localização da sua transportadora. Se a etiqueta estiver danificada ou se perder, aceda a **Overview > Download shipping label** (Descrição geral > Transferir etiqueta de envio) e transfira uma etiqueta de devolução nova.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Posso pegar o meu pedido de caixa de dados? Posso devolver os discos através de um porta-aviões que escolho?
A. Sim. A Microsoft também oferece envios auto-geridos apenas na região do Gov dos EUA. Ao fazer a encomenda do Disco caixa de dados, pode escolher a opção de envio auto-gerida. Para recolher a sua encomenda de disco de caixa de dados, tome os seguintes passos:
    
1. Depois de fazer a encomenda, a encomenda é processada e os discos são preparados. Será notificado através de um e-mail que o seu pedido está pronto para ser recolhido. 
2. Assim que a encomenda estiver pronta para ser recolhida, vá ao seu pedido no portal Azure e navegue até à lâmina **de visão geral.** 
3. Verá uma notificação com um código no portal Azure. Envie um e-mail à equipa de [Operações da Caixa de Dados Azure](mailto:adbops@microsoft.com) e forneça-lhes o código. A equipa fornecerá a localização e marcará uma data e hora de recolha. Deve ligar para a equipa no prazo de 5 dias úteis após receber a notificação por e-mail.

Uma vez concluída a cópia e validação dos dados, tome os seguintes passos para devolver o seu disco:

1. Após a validação de dados ser concluída, desligue os discos. Remova os cabos de ligação.
2. Embrulhe todos os discos e os cabos de ligação com um invólucro bolha e coloque-os na caixa de transporte. Podem aplicar-se custos se faltarem os acessórios.

    - Reutilize a embalagem do envio inicial. Recomendamos que embrulhe os discos em plástico-bolha e que o prenda bem.
    - Assegure que o plástico-bolha envolve bem os discos para reduzir os movimentos dentro da caixa.
3. Vá à **lâmina de visão geral** para o seu pedido no portal Azure. Deve ver uma notificação com um código.
4. Utilize esse código e envie um e-mail à [equipa de Operações da Caixa de Dados Azure](mailto:adbops@microsoft.com) e forneça-lhes o código. Eles fornecer-lhe-iam informações sobre onde e quando deixar os discos.


## <a name="migrate-data"></a>Migrar dados

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>P. Qual é o tamanho máximo de dados que podem ser utilizados com os discos do Data Box Disk?  
A.  A solução Data Box Disk pode ter até cinco discos com uma capacidade utilizável máxima de 35 TB. Os discos propriamente ditos têm 8 TB (7 TB utilizáveis).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>P. Quais são os tamanhos máximos de blobs de blocos e de blobs de páginas que os discos do Data Box Disk suportam? 
A.  Os tamanhos máximos são regidos pelos limites do Armazenamento do Azure. O tamanho máximo dos blobs de blocos é de, aproximadamente, 4,768 TiB e o dos blobs de páginas é de 8 TiB. Para obter mais informações, consulte [os objetivos de Escalabilidade e desempenho para o armazenamento blob.](../storage/blobs/scalability-targets.md)

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>P. Qual é a velocidade da transferência de dados do Data Box Disk?
A. Nos testes com discos ligados por USB 3.0, o desempenho dos discos foi de 430 MB/s. A velocidade real pode variar consoante o tamanho do ficheiro utilizado. Para ficheiros mais pequenos, o desempenho poderá ser mais lento.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>P. Como posso saber que os meus dados estão seguros durante o transporte? 
A.  Os discos do Data Box Disk são encriptados com a encriptação BitLocker AES de 128 bits e a chave de acesso só está disponível no portal do Azure. Para obter a chave de acesso, inicie sessão no portal do Azure com as credenciais da sua conta. Indique essa chave de acesso quando executar a ferramenta de desbloqueio do Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>P. Como posso copiar os dados para os discos do Data Box Disk? 
A.  Utilize uma ferramenta de cópia SMB, `Robocopy` `Diskboss` como, ou até mesmo o Explorador de Ficheiros do Windows, arraste e larga para copiar dados em discos.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>P. Estão disponíveis sugestões para acelerar a cópia dos dados?
A.  Para acelerar o processo de cópia:

- Utilize vários fluxos de cópia de dados. Por exemplo, `Robocopy` com, use a opção multi-leitura. Para obter informações sobre o comando exato utilizado, aceda a [Tutorial: Copy data to Azure Data Box Disk and verify](data-box-disk-deploy-copy-data.md#copy-data-to-disks) (Tutorial: Copiar dados para o Azure Data Box Disk e verificar).
- Utilize várias sessões.
- Em vez de copiar através de uma partilha de rede (em que pode estar limitado pelas velocidades da rede), certifique-se de que os dados residem localmente no computador ao qual os discos vão ser ligados.
- Certifique-se de que está a utilizar USB 3.0 ou mais tarde durante todo o processo de cópia. Descarregue e utilize a [ `USBView` ferramenta](/windows-hardware/drivers/debugger/usbview) para identificar os controladores USB e os dispositivos USB ligados ao computador.
- Compare o desempenho do computador utilizado para copiar os dados. Descarregue e utilize a [ `Bluestop` `FIO` ferramenta](https://ci.appveyor.com/project/axboe/fio) para comparar o desempenho do hardware do servidor. Selecione a mais recente construção x86 ou x64, selecione o **separador Artefactos** e descarregue o MSI.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>P. Como posso acelerar os dados se os dados de origem tiverem ficheiros pequenos (KBs ou poucos MBs)?
A.  Para acelerar o processo de cópia:

- Crie um VHDx local num armazenamento rápido ou crie um VHD vazio no HDD/SSD (mais lento).
- Monte-o numa VM.
- Copie ficheiros para o disco do VM.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>P. Posso utilizar várias contas de armazenamento com os discos do Data Box Disk?
A.  N.º Atualmente, só é suportada uma conta de armazenamento, geral ou clássica, para os discos do Data Box Disk. São suportados tanto os blobs de acesso frequente, como os de acesso esporádico. Atualmente, apenas as contas de armazenamento nos EUA, Europa Ocidental e Norte da Europa na nuvem pública de Azure são apoiadas.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>P. Qual é o conjunto de ferramentas disponível para os meus dados com discos de caixa de dados?
A. O conjunto de ferramentas disponível com o Disco caixa de dados contém três ferramentas:
 - **Ferramenta Desbloqueia a caixa de dados**: Utilize esta ferramenta para desbloquear os discos encriptados que são enviados da Microsoft. Ao desbloquear os discos utilizando a ferramenta, é necessário fornecer uma chave-passe disponível na ordem do disco da Caixa de Dados no portal Azure. 
 - **Ferramenta de validação do disco de caixa de dados**: Utilize esta ferramenta para validar o tamanho, formato e nomes blob de acordo com as convenções de nomeação Azure. Também gera datas de verificação para os dados copiados, que são depois utilizados para verificar os dados enviados para o Azure.
 - **Ferramenta Copy Split Copy da Caixa de Dados**: Utilize esta ferramenta quando estiver a utilizar vários discos e tenha um conjunto de dados grande que precisa de ser dividido e copiado em todos os discos. Esta ferramenta encontra-se atualmente disponível para o Windows. Esta ferramenta não é suportada com discos geridos. Esta ferramenta valida os dados à medida que os copia, para que possa saltar o passo de validação ao utilizar esta ferramenta.

O toolset está disponível tanto para Windows como Linux. Pode baixar o toolset aqui:
- [Transferir o conjunto de ferramentas do Data Box Disk para Windows](https://aka.ms/databoxdisktoolswin) 
- [Transferir o conjunto de ferramentas do Data Box Disk para Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>P. Posso utilizar o Disco de Caixa de Dados para transferir dados para ficheiros Azure e, em seguida, utilizar os dados com o Azure File Sync? 
A. Os Ficheiros Azure são suportados com o Disco de Caixa de Dados, mas não funcionarão bem com o Azure File Sync. Os metadados não são retidos se os dados do ficheiro forem utilizados com o Azure File Sync.


## <a name="verify-and-upload"></a>Verificar e carregar

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>P. Quando posso aceder aos meus dados em Azure depois de enviar os discos de volta? 
A.  Quando o estado da encomenda para Cópia de Dados aparecer como concluído, deverá conseguir aceder aos seus dados de imediato.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>P. Depois do carregamento, onde é que os meus dados ficam localizados no Azure?
A.  Quando copia dados nas pastas *BlockBlob* e *PageBlob* no seu disco, é criado um contentor na conta de armazenamento para cada subpasta nas pastas *BlockBlob* e *PageBlob*. Se copiou diretamente os ficheiros nas pastas *BlockBlob* e *PageBlob,* os ficheiros *encontram-se* num recipiente predefinido $root na conta de Armazenamento Azure. Quando copia os dados numa pasta sob pasta *AzureFile,* é criada uma partilha de ficheiros.

### <a name="q-i-just-noticed-that-i-didnt-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>P. Acabei de notar que não segui os requisitos de nomeação do Azure para os meus contentores. O carregamento dos meus dados para o Azure vai falhar?
A. Quaisquer letras maiúsculas nos nomes dos seus recipientes são automaticamente convertidas em minúsculas. Se os nomes não forem conformes de outras formas - por exemplo, contêm caracteres especiais ou outras línguas - o upload falhará. Para obter mais informações, aceda a [Convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>P. Como posso verificar os dados que copiei para vários discos do Data Box Disk?
A.  Após a conclusão da cópia dos dados, recomendamos que execute `DataBoxDiskValidation.cmd` fornecido na pasta *DataBoxDiskImport* para gerar somas de verificação para validação. Se tiver vários discos, tem de abrir uma janela de comando por disco e executar este comando. Tenha em conta que esta operação pode demorar muito tempo (na ordem das horas), dependendo do tamanho dos dados.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>P. O que acontece aos dados depois de devolver os discos?
A.  Após a conclusão da cópia dos dados para o Azure, os dados dos discos são apagados em segurança, de acordo com as diretrizes SP 800-88 Revision 1 da NIST.  

### <a name="q-how-is-my-data-protected-during-transit"></a>P. Como é que os meus dados são protegidos durante o transporte? 
A.  Os Discos de Caixa de Dados são encriptados com encriptação AES-128 Microsoft BitLocker, e uma única chave-passe é necessária para desbloquear todos os discos e aceder aos dados.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>P. Se adicionar mais dados aos discos do Data Box Disk, tenho de voltar a executar a validação da soma de verificação?
A. Sim. Se decidir validar os seus dados (recomendamos que o faça!), tem de voltar a executar a validação, caso tenha adicionado mais dados aos discos.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>P. Utilizei todos os discos para transferir os dados e preciso de encomendar mais. Existe alguma forma de fazer a encomenda rapidamente?
A. Pode clonar a encomenda anterior. A clonagem cria a mesma encomenda e permite-lhe editar os detalhes da mesma, sem ter de voltar a introduzir as informações de morada, contacto e notificação.

### <a name="q-i-copied-data-to-the-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-how-can-i-locate-it"></a>P. Copiei dados para a pasta ManageDisk. Não vejo discos geridos com o grupo de recursos especificado para discos geridos. Os meus dados foram enviados para o Azure? Como posso localizá-lo?
A. Sim. Os seus dados foram enviados para o Azure, mas se não vir nenhum disco gerido com os grupos de recursos especificados, é provável que os dados não fossem válidos. Se as bolhas de página, as bolhas de blocos, os ficheiros Azure ou os discos geridos não forem válidos, irão para as seguintes pastas:
 - As bolhas de página irão para um recipiente de blocos blob começando com *databoxdisk-invalid-pb-*.
 - Os Ficheiros Azure irão para um recipiente de blocos blob começando com *databoxdisk-invalid-af-*.
 - Os discos geridos irão para um contentor de blocos blob começando com *databoxdisk-invalid-md-*.

## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos do [sistema de disco de caixa de dados.](data-box-disk-system-requirements.md)
- Compreenda os [Limites do Data Box Disk](data-box-disk-limits.md).
- Implemente rapidamente o [Azure Data Box Disk](data-box-disk-quickstart-portal.md) no portal do Azure.