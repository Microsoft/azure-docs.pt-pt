---
title: FAQ do Microsoft Azure Data Box Disk | Microsoft Docs em dados
description: Contém perguntas frequentes e respostas para Disco do Azure Data Box, uma solução de nuvem que permite transferir grandes quantidades de dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 08/15/2019
ms.author: alkohli
ms.openlocfilehash: 1361224fb59a32ca227c5ea1e6cc92a4b0fdff6e
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541808"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Disco do Azure Data Box: Perguntas Mais Frequentes

A solução na cloud Microsoft Azure Data Box Disk permite-lhe enviar terabytes de dados para o Azure de forma rápida, económica e fiável. Estas FAQ contêm perguntas e respostas para dúvidas que possa ter ao utilizar o Azure Data Box Disk no portal do Azure. 

As perguntas e respostas estão organizadas pelas seguintes categorias:

- Acerca do serviço
- Configurar e ligar 
- Acompanhar o estado
- Migrar dados 
- Verificar e carregar dados 


## <a name="about-the-service"></a>Acerca do serviço

### <a name="q-what-is-azure-data-box-service"></a>P. O que é o serviço Azure Data Box? 
R.  O serviço Azure Data Box foi concebido para ingestão de dados offline. Este serviço gere uma panóplia de produtos feitos à medida para o transporte de dados para diferentes capacidades de armazenamento. 

### <a name="q-what-are-azure-data-box-disks"></a>P. O que são os discos do Azure Data Box Disk?
A. Os discos do Azure Data Box Disk permitem a transferência rápida, económica e segura de terabytes de dados de e para o Azure. A Microsoft envia-lhe um a cinco discos, com uma capacidade de armazenamento máxima de 35 TB. Pode configurar, ligar e desbloquear facilmente estes discos através do serviço Data Box no portal do Azure.  

Os discos são encriptados com a Encriptação de Unidade BitLocker da Microsoft e as suas chaves de encriptação são geridas no portal do Azure. Depois, os dados são copiados dos servidores do cliente. No datacenter, a Microsoft migra os seus dados da unidade para a cloud através de uma ligação de carregamento de rede rápida e privada e carrega-os para o Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>P. Quando devo utilizar os discos do Data Box Disk?
R. Se tiver 40 TB (ou menos) de dados e quiser transferi-los para o Azure, pode tirar partido dos discos do Data Box Disk.

### <a name="q-what-is-the-price-of-data-box-disks"></a>P. Qual é o preço dos discos do Data Box Disk?
R. Para obter informações sobre o preço de Data Box discos, acesse a [página de preços](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>P. Como recebo os discos do Data Box Disk? 
R.  Para obter Azure Data Box discos, faça logon no portal do Azure e crie uma ordem de Data Box para discos. Indique as suas informações de contacto e os seus detalhes de notificação. Depois de fazer a encomenda, os discos são-lhe enviados num prazo de dez dias, com base na disponibilidade.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>P. Qual é a quantidade máxima de dados que posso transferir com os discos do Data Box Disk numa instância?
A. Para cinco discos de 8 TB cada (7 TB de capacidade utilizável), a capacidade utilizável máxima são 35 TB. Assim, pode transferir 35 TB de dados numa instância. Para transferir mais dados, tem de encomendar mais discos.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>P. Como posso saber se os discos do Data Box Disk estão disponíveis na minha região? 
R.  Para ver onde os discos Data Box estão disponíveis no momento, vá para a [disponibilidade da região](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>P. Em que regiões posso armazenar dados com os discos do Data Box Disk?
R. Disco do Data Box tem suporte para todas as regiões dentro dos EUA, Canadá, Austrália, Europa Ocidental e Europa Setentrional, Coréia e Japão. Só são suportadas as regiões de cloud pública do Azure. O Azure Government e as outras clouds soberanas não são suportadas.

### <a name="q-will-my-data-box-disk-cross-country-borders-during-shipping"></a>P. O meu Disco do Data Boxá bordas entre países durante o envio?
R. Os Disco do Data Box são enviados de dentro do mesmo país que o seu destino e não atravessam nenhuma borda internacional. A única exceção é para pedidos na União Europeia (UE), em que os discos podem enviar de e para qualquer país da UE.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>P. Quem devo contactar se me deparar com problemas com os discos do Data Box Disk?
R. Se você encontrar problemas com Data Box discos, [entre em contato com suporte da Microsoft](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support).

## <a name="configure-and-connect"></a>Configurar e ligar
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>P. Posso especificar o número de discos do Data Box Disk na encomenda?
A.  Não. Recebe discos de 8 TB (num máximo de cinco discos), dependendo do tamanho dos seus dados e da disponibilidade de discos.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>P. Como posso desbloquear os discos do Data Box Disk? 
A.  No portal do Azure, aceda à encomenda do Data Box Disk e navegue para **Detalhes do dispositivo**. Copie a chave de acesso. Transfira e instale a ferramenta de desbloqueio do Data Box Disk do portal do Azure para o seu sistema operativo. Execute a ferramenta no computador que tem os dados que pretende copiar para os discos. Indique a chave de acesso para desbloquear os discos. A mesma chave de acesso desbloqueia todos os discos. 

Para obter instruções passo a passo, aceda a [Desbloquear discos num cliente Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) ou [Desbloquear discos num cliente Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>P. Posso utilizar um computador anfitrião Linux para ligar e copiar os dados para os discos do Data Box Disk?
R.  Sim. Os clientes Linux e Windows podem ser utilizados para ligar e copiar os dados para os discos do Data Box. Para obter mais informações, aceda à lista de [Sistemas operativos suportados](data-box-disk-system-requirements.md) do seu computador anfitrião.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>P. Os meus discos foram enviados, mas quero cancelar esta encomenda. Por que é que o botão de cancelamento não está disponível?
A.  Só pode cancelar a encomenda depois de os discos terem sido encomendados e antes de serem enviados. Após o envio, já não pode cancelar a encomenda. No entanto, você pode retornar seus discos em um encargo. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>P. Posso ligar vários discos do Data Box Disk ao mesmo computador anfitrião para transferir os dados?
A. Sim. É possível ligar vários discos do Data Box ao mesmo computador anfitrião para transferir os dados e podem ser executados múltiplos trabalhos de cópia em paralelo.

## <a name="track-status"></a>Acompanhar o estado

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>P. Como posso fazer o acompanhamento desde o momento em que fiz a encomenda até à devolução dos discos? 
A.  Pode acompanhar o estado da encomenda do Data Box Disk no portal do Azure. Quando cria a encomenda, também lhe é pedido que indique um e-mail para notificações. Se o tiver indicado, todas as alterações ao estado da encomenda são-lhe notificadas por e-mail. Mais informações sobre como [configurar os e-mails de notificações](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>P. Como posso devolver os discos? 
A.  A Microsoft fornece uma etiqueta de envio com o Data Box Disk na embalagem. Afixe a etiqueta à caixa de envio e entregue a embalagem fechada na localização da sua transportadora. Se a etiqueta estiver danificada ou se perder, aceda a **Overview > Download shipping label** (Descrição geral > Transferir etiqueta de envio) e transfira uma etiqueta de devolução nova.

## <a name="migrate-data"></a>Migrar dados

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>P. Qual é o tamanho máximo de dados que podem ser utilizados com os discos do Data Box Disk?  
A.  A solução Data Box Disk pode ter até cinco discos com uma capacidade utilizável máxima de 35 TB. Os discos propriamente ditos têm 8 TB (7 TB utilizáveis).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>P. Quais são os tamanhos máximos de blobs de blocos e de blobs de páginas que os discos do Data Box Disk suportam? 
A.  Os tamanhos máximos são regidos pelos limites do Armazenamento do Azure. O tamanho máximo dos blobs de blocos é de, aproximadamente, 4,768 TiB e o dos blobs de páginas é de 8 TiB. Para obter mais informações, aceda a [Azure Storage Scalability and Performance Targets](../storage/common/storage-scalability-targets.md) (Metas de Desempenho e Escalabilidade do Armazenamento do Azure).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>P. Qual é a velocidade da transferência de dados do Data Box Disk?
A. Nos testes com discos ligados por USB 3.0, o desempenho dos discos foi de 430 MB/s. A velocidade real pode variar consoante o tamanho do ficheiro utilizado. Para ficheiros mais pequenos, o desempenho poderá ser mais lento.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>P. Como posso saber que os meus dados estão seguros durante o transporte? 
A.  Os discos do Data Box Disk são encriptados com a encriptação BitLocker AES de 128 bits e a chave de acesso só está disponível no portal do Azure. Para obter a chave de acesso, inicie sessão no portal do Azure com as credenciais da sua conta. Indique essa chave de acesso quando executar a ferramenta de desbloqueio do Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>P. Como posso copiar os dados para os discos do Data Box Disk? 
A.  Utilize uma ferramenta de cópias SMB, como o Robocopy, o Diskboss ou até mesmo o arrastar e largar do Explorador de Ficheiros do Windows, para copiar os dados para os discos.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>P. Estão disponíveis sugestões para acelerar a cópia dos dados?
A.  Para acelerar o processo de cópia:

- Utilize vários fluxos de cópia de dados. Por exemplo, com o Robocopy, utilize a opção multithreaded. Para obter mais informações sobre o comando exato usado, acesse [tutorial: Copie dados para Disco do Azure Data Box e verifique](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Utilize várias sessões.
- Em vez de copiar através de uma partilha de rede (em que pode estar limitado pelas velocidades da rede), certifique-se de que os dados residem localmente no computador ao qual os discos vão ser ligados.
- Confirme que está a utilizar o USB 3.0 ou posterior ao longo de todo o processo de cópia. Transfira e utilize a [ferramenta USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) para identificar os controladores de USB e os dispositivos USB ligados ao computador.
- Compare o desempenho do computador utilizado para copiar os dados. Transfira e utilize a [ferramenta Bluestop FIO](https://bluestop.org/fio/) para comparar o desempenho do hardware do servidor.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>P. Como posso acelerar os dados se os dados de origem tiverem ficheiros pequenos (KBs ou poucos MBs)?
A.  Para acelerar o processo de cópia:

- Crie um VHDx local num armazenamento rápido ou crie um VHD vazio no HDD/SSD (mais lento).
- Monte-o numa VM.
- Copie os ficheiros para o disco da VM.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>P. Posso utilizar várias contas de armazenamento com os discos do Data Box Disk?
A.  Não. Atualmente, só é suportada uma conta de armazenamento, geral ou clássica, para os discos do Data Box Disk. São suportados tanto os blobs de acesso frequente, como os de acesso esporádico. Atualmente, somente as contas de armazenamento nos EUA, Europa Ocidental e Europa Setentrional na nuvem pública do Azure têm suporte.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>P. O que é o conjunto de ferramentas disponível para meus dados com Data Box discos?
R. O conjunto de ferramentas disponível com o Disco do Data Box contém três ferramentas:
 - **Ferramenta**de desbloqueio de disco do data Box: Use essa ferramenta para desbloquear os discos criptografados que são enviados da Microsoft. Ao desbloquear os discos usando a ferramenta, você precisa fornecer uma chave de acesso disponível na ordem de Disco do Data Box no portal do Azure. 
 - **Ferramenta de validação de disco do data Box**: Use essa ferramenta para validar o tamanho, o formato e os nomes de blob de acordo com as convenções de nomenclatura do Azure. Ele também gera somas de verificação para os dados copiados que são usados para verificar os dados carregados no Azure.
 - **Ferramenta de cópia dividida disco do data Box**: Use essa ferramenta quando você estiver usando vários discos e tiver um grande conjunto de grandes que precise ser dividido e copiado em todos os discos. Essa ferramenta está disponível no momento para Windows. Não há suporte para essa ferramenta com discos gerenciados. Essa ferramenta também é validada à medida que copia os dados, portanto, você pode ignorar a etapa de validação ao usar essa ferramenta.

O conjunto de ferramentas está disponível para Windows e Linux. Você pode baixar o conjunto de ferramentas aqui:
- [Transferir o conjunto de ferramentas do Data Box Disk para Windows](https://aka.ms/databoxdisktoolswin) 
- [Transferir o conjunto de ferramentas do Data Box Disk para Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>P. Posso usar Disco do Data Box para transferir dados para arquivos do Azure e, em seguida, usar os dados com Sincronização de Arquivos do Azure? 
R. Os arquivos do Azure têm suporte com Disco do Data Box, mas não funcionarão bem com Sincronização de Arquivos do Azure. Os metadados não serão mantidos se os dados do arquivo forem usados com Sincronização de Arquivos do Azure.


## <a name="verify-and-upload"></a>Verificar e carregar

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>P. Quanto tempo depois de devolver os discos é que posso aceder aos meus dados no Azure? 
A.  Quando o estado da encomenda para Cópia de Dados aparecer como concluído, deverá conseguir aceder aos seus dados de imediato.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>P. Depois do carregamento, onde é que os meus dados ficam localizados no Azure?
A.  Quando copia dados nas pastas *BlockBlob* e *PageBlob* no seu disco, é criado um contentor na conta de armazenamento para cada subpasta nas pastas *BlockBlob* e *PageBlob*. Se tiver copiado os ficheiros nas pastas *BlockBlob* e *PageBlob* diretamente, aqueles estão num contentor predefinido, *$root*, na conta de armazenamento do Azure. Quando você copia os dados em uma pasta na pasta *do azurefile* , um FileShare é criado.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>P. Acabei de reparar que não segui os requisitos de nomenclatura do Azure nos meus contentores. O carregamento dos meus dados para o Azure vai falhar?
A. Se os nomes dos contentores tiverem letras maiúsculas, as mesmas são convertidas automaticamente em minúsculas. Se os nomes não estiverem em conformidade por outros motivos (carateres especiais, outros idiomas, etc.), o carregamento falhará. Para obter mais informações, aceda a [Convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>P. Como posso verificar os dados que copiei para vários discos do Data Box Disk?
R.  Após a conclusão da cópia dos dados, recomendamos que execute `DataBoxDiskValidation.cmd` fornecido na pasta *DataBoxDiskImport* para gerar somas de verificação para validação. Se tiver vários discos, tem de abrir uma janela de comando por disco e executar este comando. Tenha em conta que esta operação pode demorar muito tempo (na ordem das horas), dependendo do tamanho dos dados.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>P. O que acontece aos dados depois de devolver os discos?
A.  Após a conclusão da cópia dos dados para o Azure, os dados dos discos são apagados em segurança, de acordo com as diretrizes SP 800-88 Revision 1 da NIST.  

### <a name="q-how-is-my-data-protected-during-transit"></a>P. Como é que os meus dados são protegidos durante o transporte? 
A.  Os discos do Data Box Disk são encriptados com a encriptação BitLocker AES-128 da Microsoft. É necessária uma chave de acesso única para desbloquear os discos e aceder aos dados.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>P. Se adicionar mais dados aos discos do Data Box Disk, tenho de voltar a executar a validação da soma de verificação?
A. Sim. Se decidir validar os seus dados (recomendamos que o faça!), tem de voltar a executar a validação, caso tenha adicionado mais dados aos discos.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>P. Utilizei todos os discos para transferir os dados e preciso de encomendar mais. Existe alguma forma de fazer a encomenda rapidamente?
A. Pode clonar a encomenda anterior. A clonagem cria a mesma encomenda e permite-lhe editar os detalhes da mesma, sem ter de voltar a introduzir as informações de morada, contacto e notificação.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>P. Copiei dados para a pasta ManagedDisk. Não vejo nenhum disco gerenciado com o grupo de recursos especificado para discos gerenciados. Meus dados foram carregados no Azure e como posso localizá-los?
R. Sim. Seus dados foram carregados no Azure, mas se você não vir discos gerenciados com os grupos de recursos especificados, é provável que os dados não sejam válidos. Se os blobs de páginas, blobs de blocos, arquivos do Azure e discos gerenciados não forem válidos, eles irão para as seguintes pastas:
 - Os blobs de páginas vão para um contêiner de blob de blocos começando com *databoxdisk-Invalid-PB-* .
 - Os arquivos do Azure vão para um contêiner de blobs de blocos começando com *databoxdisk-Invalid-AF-* .
 - Os discos gerenciados seriam para um contêiner de blob de blocos começando com *databoxdisk-Invalid-MD-* .

## <a name="next-steps"></a>Passos Seguintes

- Examine os [requisitos de sistema do disco do data Box](data-box-disk-system-requirements.md).
- Compreenda os [Limites do Data Box Disk](data-box-disk-limits.md).
- Implemente rapidamente o [Azure Data Box Disk](data-box-disk-quickstart-portal.md) no portal do Azure.
