---
title: Problemas de resolução de problemas com imagens partilhadas em Azure
description: Aprenda a resolver problemas com galerias de imagem partilhadas.
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 015fa201fe1c31dde2e30c2fe689ac13452b1b01
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607597"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Resolução de problemas partilhadas galerias de imagens em Azure

Se tiver problemas em realizar quaisquer operações em galerias de imagem partilhadas, definições de imagem e versões de imagem, volte a executar o comando falhado no modo depurado. Ativa o modo de depuração passando o `--debug` interruptor com o Azure CLI e o `-Debug` interruptor com o PowerShell. Depois de localizar o erro, siga este artigo para resolver os problemas.


## <a name="creating-or-modifying-a-gallery"></a>Criar ou modificar uma galeria ##

**Mensagem**: *O nome da galeria é inválido. Os caracteres permitidos são caracteres alfanuméricos ingleses, com sublinhados, e períodos permitidos no meio, até 80 caracteres no total. Todos os outros caracteres especiais, incluindo traços, são proibidos.*  
**Causa**: O nome da galeria não satisfaz os requisitos de nomeação.  
**Solução alternativa**: Escolha um nome que satisfaça as seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras, números, sublinhados e períodos em inglês
- Começa e termina com letras ou números ingleses

**Mensagem**: O nome da *entidade 'galleryName' é inválido de acordo com a sua regra de validação: \_ ^[^\W][\w-. \_ ] {0,79} (?<! [-.]) $.*  
**Causa**: O nome da galeria não satisfaz os requisitos de nomeação.  
**Solução:** Escolha um nome para a galeria que satisfaça as seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras, números, sublinhados e períodos em inglês
- Começa e termina com letras ou números ingleses

**Mensagem**: *O nome de recurso fornecido <galeriaName tem estes caracteres \> inválidos: <carácter \> . O nome não pode terminar com caracteres: \> <personagem*  
**Causa**: O nome da galeria termina com um período ou sublinhado.  
**Solução:** Escolha um nome para a galeria que satisfaça as seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras, números, sublinhados e períodos em inglês
- Começa e termina com letras ou números ingleses

**Mensagem**: *A localização fornecida <região \> não está disponível para o tipo de recurso 'Microsoft.Compute/galleries'. A lista das regiões disponíveis para o tipo de recurso é ...*  
**Causa**: A região especificada para a galeria está incorreta ou requer um pedido de acesso.  
**Solução :** Verifique se o nome da região está correto. Se o nome da região estiver correto, envie [um pedido de acesso](/troubleshoot/azure/general/region-access-request-process) para a região.

**Mensagem**: *Não pode eliminar o recurso antes de os recursos aninhados serem eliminados.*  
**Porque:** Tentou apagar uma galeria que contém pelo menos uma definição de imagem existente. Uma galeria deve estar vazia antes de poder ser apagada.  
**Solução alternativa**: Elimine todas as definições de imagem no interior da galeria e, em seguida, proceda à eliminação da galeria. Se a definição de imagem contiver versões de imagem, deve eliminar as versões de imagem antes de eliminar as definições de imagem.

**Mensagem**: *O nome da galeria '<galleryName ' não é único dentro da \> subscrição <subscriptionID> '. Por favor, escolha outro nome da galeria.*  
**Causa:** Tem uma galeria existente com o mesmo nome e tentou criar outra galeria com o mesmo nome.  
**Solução alternativa**: Escolha um nome diferente para a galeria.

**Mensagem**: *O recurso <galeriaName \> já existe na localização <região \_ 1 no grupo de \> recursos <grupo de recursosGroup \> . Um recurso com o mesmo nome não pode ser criado na localização <região \_ 2 \> . Por favor, selecione um novo nome de recurso.*  
**Causa:** Tem uma galeria existente com o mesmo nome e tentou criar outra galeria com o mesmo nome.  
**Solução alternativa**: Escolha um nome diferente para a galeria.

## <a name="creating-or-modifying-image-definitions"></a>Criar ou modificar definições de imagem ##

**Mensagem**: *Não é permitido alterar a propriedade 'galleryImage.properties.<\> propriedade'.*  
**Causa:** Tentou alterar o tipo de SO, estado de SO, geração Hyper-V, oferta, editora ou SKU. Não é permitido alterar qualquer uma destas propriedades.  
**Solução alternativa**: Criar uma nova definição de imagem em vez disso.

**Mensagem**: *O recurso <galeriaName/imagemDefinitionName \> já existe na localização <região \_ 1 no grupo de recursos \> <grupo de recursosGroup \> . Um recurso com o mesmo nome não pode ser criado na localização <região \_ 2 \> . Por favor, selecione um novo nome de recurso.*  
**Causa**: Tem uma definição de imagem existente na mesma galeria e grupo de recursos com o mesmo nome. Tentou criar outra definição de imagem com o mesmo nome e na mesma galeria, mas numa região diferente.  
**Solução alternativa**: Use um nome diferente para a definição de imagem, ou coloque a definição de imagem numa galeria ou grupo de recursos diferente.

**Mensagem**: *O nome de recurso fornecido <galeriaName \> /<imagemDefinitionName \> tem estes caracteres inválidos: <carácter \> . O nome não pode terminar com \> caracteres: <personagem*  
**Causa**: A imagem <DefinitionName \> o nome termina com um período ou sublinhado.  
**Solução alternativa**: Escolha um nome para a definição de imagem que satisfaça as seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras, números, sublinhados, hífenes e períodos
- Começa e termina com letras ou números ingleses.

**Mensagem**: *O nome da entidade <imagemDefinitionName é \> inválido de acordo com a sua regra de validação: \_ ^[^W][ \\ \\ \_ w-. {0,79} (?<! [-.]) $"*  
**Causa**: A imagem <DefinitionName \> o nome termina com um período ou sublinhado.  
**Solução alternativa**: Escolha um nome para a definição de imagem que satisfaça as seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras, números, sublinhados, hífenes e períodos
- Começa e termina com letras ou números ingleses

**Mensagem**: *Galeria de nomes de activosImage.properties.identifier.<propriedade \> não é válida. Não pode estar vazio. Os caracteres permitidos são letras maiúsculas ou minúsculas, dígitos, hífen(-), período (.), sublinhado ( \_ ). Os nomes não podem terminar com o período(.). O comprimento do nome não pode exceder <\> caracteres numeros.*  
**Causa**: O valor do editor, oferta ou SKU não satisfaz os requisitos de nomeação.  
**Solução alternativa**: Escolha um valor que satisfaça as seguintes condições: 
- Tem um limite de 128 caracteres para editor ou limite de 64 caracteres para oferta e SKU
- Contém apenas letras, números, hífens, sublinhados e períodos
- Não termina com um período

**Mensagem**: *Não pode efetuar a operação solicitada em recursos aninhados. Recurso dos pais <galeria Não \> encontrado.*  
**Causa**: Não existe galeria com o nome <\> galeriaName no atual grupo de subscrição e recursos.  
**Solução :** Verifique se os nomes da galeria, subscrição e grupo de recursos estão corretos. Caso contrário, crie uma nova galeria chamada <\> galeriaName.

**Mensagem**: *A localização fornecida <região \> não está disponível para o tipo de recurso 'Microsoft.Compute/galleries'. A lista das regiões disponíveis para o tipo de recurso é ...*  
**Causa**: O nome <região \> está incorreto ou requer um pedido de acesso.  
**Solução :** Verifique se o nome da região está escrito corretamente. Pode comandar este comando para ver a que regiões tem acesso. Se a região não estiver na lista, apresente [um pedido de acesso.](/troubleshoot/azure/general/region-access-request-process)

**Mensagem**: *Não é possível serializar valor: <valor \> como tipo: 'iso-8601'., ISO8601Error: ISO 8601 time designattor 'T' desaparecido. Incapaz de analisar a cadeia de datas \> <valor*  
**Causa**: O valor fornecido à propriedade não está devidamente formatado como data.  
**Solução :** Forneça uma data no formato yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *Não foi possível converter o string para DateTimeOffset: <valor \> . Propriedades do caminho'<\> propriedade'*  
**Causa**: O valor fornecido à propriedade não está devidamente formatado como data.  
**Solução :** Forneça uma data no formato yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *EndOfLifeDate deve ser definido para uma data futura.*  
**Causa**: A propriedade de data de fim de vida não está devidamente formatada como uma data que é depois da data de hoje.  
**Solução :** Forneça uma data no formato yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *argumento --<propriedade \> : valor int inválido: valor \> <*  
**Causa:**<propriedade \> aceita apenas valores inteiros, e <valor \> não é um inteiro.  
**Solução alternativa**: Escolha um valor inteiro.

**Mensagem**: *O valor mínimo da propriedade <não deve ser superior ao valor máximo de <propriedade \> \> .*  
**Causa**: O valor mínimo previsto para <propriedade \> é superior ao valor máximo previsto para <\> propriedade.  
**Alternativa :** Alterar os valores de modo a que o mínimo seja inferior ou igual ao máximo.

**Mensagem**: *Imagem de galeria: <imagemDefinitionName \> identificado por (publisher:<Publisher \> , offer:<Offer \> , sku:<\> SKU) já existe. Escolha uma editora diferente, oferta, combinação de sku.*  
**Causa**: Tentou criar uma nova definição de imagem com a mesma editora, oferta e trigémeo SKU como uma definição de imagem existente na mesma galeria.  
**Resumo :** Dentro de uma galeria, todas as definições de imagem devem ter uma combinação única de editor, oferta e SKU. Escolha uma combinação única, ou escolha uma nova galeria e crie novamente a definição de imagem.

**Mensagem**: *Não pode eliminar o recurso antes de os recursos aninhados serem eliminados.*  
**Porque:** Tentou eliminar uma definição de imagem que contém versões de imagem. Uma definição de imagem deve estar vazia antes de poder ser eliminada.  
**Solução alternativa**: Elimine todas as versões de imagem dentro da definição de imagem e, em seguida, proceda à eliminação da definição de imagem.

**Mensagem**: *Não é possível ligar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para tipo <propriedadeType \> . Não é possível comparar o nome do identificador <valor \> a um nome enumerador válido. Especifique um dos seguintes nomes enumeradores e tente novamente: <escolha \_ 1 \> , <escolha \_ 2 , \> ...*  
**Causa**: A propriedade tem uma lista restrita de valores possíveis, e <valor \> não é um deles.  
**Resumo :** Escolha um dos valores de escolha <\> possíveis.

**Mensagem**: *Não é possível ligar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para tipo &quot; System.DateTime &quot;*  
**Causa**: O valor fornecido à propriedade não está devidamente formatado como data.  
**Solução :** Forneça uma data no formato yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *Não é possível ligar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para tipo &quot; Sistema.Int32 &quot;*  
**Causa:**<propriedade \> aceita apenas valores inteiros, e <valor \> não é um inteiro.  
**Solução alternativa**: Escolha um valor inteiro.

**Mensagem**: *O tipo de conta de armazenamento ZRS não é suportado nesta região.*  
**Causa**: Você escolheu o armazenamento padrão de zona redundante (ZRS) em uma região que ainda não o suporta.  
**Solução alternativa**: Altere o tipo de conta de armazenamento para **\_ LRS Premium** ou **\_ LRS Standard**. Consulte a nossa documentação para obter a mais recente [lista de regiões](../storage/common/storage-redundancy.md#zone-redundant-storage) com pré-visualização ZRS ativada.

## <a name="creating-or-updating-image-versions"></a>Criar ou atualizar versões de imagem ##

**Mensagem**: *A localização fornecida <região \> não está disponível para o tipo de recurso 'Microsoft.Compute/galleries'. A lista das regiões disponíveis para o tipo de recurso é ...*  
**Causa**: O nome <região \> está incorreto ou requer um pedido de acesso.  
**Solução :** Verifique se o nome da região está escrito corretamente. Pode comandar este comando para ver a que regiões tem acesso. Se a região não estiver na lista, apresente [um pedido de acesso.](/troubleshoot/azure/general/region-access-request-process)

**Mensagem**: *Não pode efetuar a operação solicitada em recursos aninhados. Recurso dos pais <galeriaName/imagemDefinitionName \> não encontrado.*  
**Causa**: Não existe galeria com o nome <galeriaName/imageDefinitionName \> no grupo de subscrição e recursos atuais.  
**Solução :** Verifique se os nomes da galeria, subscrição e grupo de recursos estão corretos. Caso contrário, crie uma nova galeria com o nome <galeriaName \> e/ou uma definição de imagem nomeada <imagemDefinitionName \> no grupo de recursos indicado.

**Mensagem**: *Não é possível ligar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para tipo &quot; System.DateTime &quot;*  
**Causa**: O valor fornecido à propriedade não está devidamente formatado como data.  
**Solução :** Forneça uma data no formato yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *Não é possível ligar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para tipo &quot; Sistema.Int32 &quot;*  
**Causa:**<propriedade \> aceita apenas valores inteiros, e <valor \> não é um inteiro.  
**Solução alternativa**: Escolha um valor inteiro.

**Mensagem**: *A versão de imagem da galeria que publica regiões de perfil <publicação As Regiões devem conter a \> localização da versão de imagem <fonteRegion \>*  
**Causa**: A localização da imagem de origem (<fonte Região \> ) deve ser incluída na lista <publicação das Regiões. \>  
**Solução alternativa**: Incluir <fonteRegion \> na lista <de publicações. \>

**Mensagem**: *O valor <valor do parâmetro <propriedade está fora de \> \> alcance. O valor deve ser entre <minValue \> e <maxValue, \> inclusive.*  
**Causa:**<valor \> está fora do intervalo de valores possíveis para <\> propriedade.  
**Solução alternativa**: Escolha um valor que esteja dentro da gama de <minValue \> e <maxValue, \> inclusive.

**Mensagem**: *Fonte <recursosID \> não é encontrado. Por favor, verifique se a fonte existe, e está na mesma região que a versão de imagem de galeria que está sendo criada.*  
**Causa**: Não existe nenhuma fonte localizada no <\> recursosID, ou a fonte em <recursosID não está na mesma \> região que a imagem da galeria que está a ser criada.  
**Resumo :** Verifique se o valoridóide de recursos <\> está correto e que a região de origem da versão de imagem de galeria é a mesma que a região do valoridid de recursos \> <.

**Mensagem**: *Não é permitida a alteração da propriedade 'galleryImageVersion.properties.storageProfile.<diskImage \> .source.id'.*  
**Causa**: A identificação de origem de uma versão de imagem de galeria não pode ser alterada após a criação.  
**Resumo :** Certifique-se de que o ID de origem é o mesmo que o ID de origem existente, altere o número de versão da versão de imagem ou elimine a versão de imagem atual e tente novamente.

**Mensagem**: *Foram detetados números de lun duplicados nos discos de dados de entrada. O número lun deve ser único para cada disco de dados.*  
**Causa:** Quando está a criar uma versão de imagem utilizando uma lista de discos e/ou instantâneos de disco, dois ou mais discos ou instantâneos de disco têm o mesmo LUN.  
**Solução alternativa**: Remova ou altere quaisquer LUNs duplicados.

**Mensagem**: *Os ids de origem duplicados encontram-se nos discos de entrada. A identificação de origem deve ser única para cada disco.*  
**Causa:** Quando está a criar uma versão de imagem utilizando uma lista de discos e/ou instantâneos de disco, dois ou mais discos ou instantâneos de disco têm o mesmo ID de recurso.  
**Solução alternativa**: Remova ou altere quaisquer IDs duplicados de fonte de disco.

**Mensagem**: *O id de propriedade <recursosID \> no caminho 'properties.storageProfile.<diskImages \> .source.id' é inválido. Espere identificação de recursos totalmente qualificado que comecem com '/subscrições/ <subscriptionID> ' ou '/fornecedores/ <resourceProviderNamespace> /'.*  
**Causa**: O valor <de recursosID \> está incorretamente formatado.  
**Resolução :** Verifique se o ID do recurso está correto.

**Mensagem**: *O id de origem: <recursosID \> deve ser uma imagem gerida, máquina virtual ou outra versão de imagem de galeria*  
**Causa**: O valor <de recursosID \> está incorretamente formatado.  
**Resumo :** Se estiver a utilizar uma versão VM, imagem gerida ou imagem de galeria como imagem de origem, verifique se o ID de recursos do VM, imagem gerida ou versão de imagem de galeria está correto.

**Mensagem**: *O id de origem: <recursoID \> deve ser um disco ou instantâneo gerido.*  
**Causa**: O valor <de recursosID \> está incorretamente formatado.  
**Resumo :** Se estiver a utilizar discos e/ou instantâneos de disco como fontes para a versão de imagem, verifique se os IDs de recursos dos discos e/ou instantâneos do disco estão corretos.

**Mensagem**: *Não é possível criar a versão de imagem de galeria a partir de: <recursosID \> uma vez que o Estado OS na imagem da galeria-mãe (<OsState \_ 1 ) \> não é <OsState \_ 2 \> .*  
**Causa**: O estado do sistema operativo (generalizado ou especializado) não corresponde ao estado do sistema operativo especificado na definição de imagem.  
**Resumo**: Escolha uma fonte baseada num VM com o estado do sistema operativo de <OsState \_ 1 ou crie uma nova definição de \> imagem para VMs com base em <OsState \_ 2 \> .

**Mensagem**: *O recurso com id '<recursosID' \> tem uma geração hipervisor diferente ['<V# \_ \> 1'] do que a imagem da galeria-mãe Hipervisor geração ['<V# \_ 2 \> ']*  
**Causa**: A geração hipervisor da versão de imagem não corresponde à geração de hipervisores especificada na definição de imagem. O sistema operativo de definição de imagem é <V# \_ \> 1, e o sistema operativo da versão de imagem está <V# \_ 2 \> .  
**Solução alternativa**: Escolha uma fonte com a mesma geração hipervisor que a definição de imagem ou crie/escolha uma nova definição de imagem que tenha a mesma geração hipervisor que a versão de imagem.

**Mensagem**: *O recurso com id '<recursosID' \> tem um tipo de SO diferente ['<OsType \_ \> 1'] do que a geração do tipo os tipo de imagem de imagem de pai ['<OsType \_ 2 \> ']*  
**Causa**: A geração hipervisor da versão de imagem não corresponde à geração de hipervisores especificada na definição de imagem. O sistema operativo de definição de imagem é <OsType \_ 1 \> , e o sistema operativo da versão de imagem é <OsType \_ 2 \> .  
**Solução alternativa**: Escolha uma fonte com o mesmo sistema operativo (Linux/Windows) como a definição de imagem ou crie/escolha uma nova definição de imagem que tenha a mesma geração do sistema operativo que a versão de imagem.

**Mensagem**: *A máquina virtual de origem <recursosID não pode conter um disco de OS \> efémero.*  
**Causa**: A fonte <recursosID \> contém um disco de OS efémero. A galeria de imagens partilhada não suporta atualmente discos efémeros de OS.  
**Solução alternativa**: Escolha uma fonte diferente com base num VM que não utilize um disco de OS efémero.

**Mensagem**: *Fonte de máquina virtual <recursosID não pode conter disco \> ['<diskID \> '] armazenado num tipo de conta UltraSSD.*  
**Causa**: O disco <diskID \> é um disco Ultra SSD. A galeria de imagens partilhada não suporta atualmente discos Ultra SSD.  
**Solução alternativa**: Utilize uma fonte que contenha apenas discos geridos Premium SSD, Standard SSD e/ou HDD standard.

**Mensagem**: *A origem da máquina virtual <recursosID \> devem ser criados a partir de Discos Geridos.*  
**Causa**: A máquina virtual em <recursosID \> utiliza discos não geridos.  
**Solução alternativa**: Utilize uma fonte baseada num VM que contenha apenas discos geridos Premium SSD, Standard SSD e/ou HDD Standard.

**Mensagem**: *Demasiados pedidos na fonte '<\> recursosID'. Por favor, reduza o número de pedidos na fonte ou aguarde algum tempo antes de voltar a tentar.*  
**Causa**: A fonte para esta versão de imagem está atualmente a ser estrangulada devido a muitos pedidos.  
**Solução alternativa**: Tente criar a versão de imagem mais tarde.

**Mensagem**: *O conjunto de encriptação do disco '<diskEncryptionSetID \> ' deve estar na mesma subscrição '<subscriçãoID' \> que o recurso da galeria.*  
**Causa**: Os conjuntos de encriptação de discos só podem ser utilizados na mesma subscrição e região em que foram criados.  
**Solução alternativa**: Criar ou utilizar um conjunto de encriptação na mesma subscrição e região que a versão de imagem.

**Mensagem**: *Fonte encriptada: '<\> resourceID' está num ID de subscrição diferente do atual da subscrição da versão de imagem de galeria '<subscriçãoID \_ 1 \> '. Por favor, recomprê-lo com uma(s) fonte(s) não encriptada ou use a subscrição da fonte '<subcriçãoID \_ 2 \> ' para criar a versão de imagem da galeria.*  
**Causa**: A galeria de imagens partilhada não suporta atualmente a criação de versões de imagem noutra subscrição a partir de outra imagem de origem se a imagem de origem estiver encriptada.  
**Solução alternativa**: Utilize uma fonte não encriptada ou crie a versão de imagem na mesma subscrição que a fonte.

**Mensagem**: *O conjunto de encriptação do disco <discoEncryptionSetID \> não foi encontrado.*  
**Causa:** A encriptação do disco pode estar incorreta.  
**Resumo :** Verifique se o ID de recurso do conjunto de encriptação do disco está correto.

**Mensagem**: *O nome da versão de imagem é inválido. O nome da versão de imagem deve seguir Major(int). Menor(int). Formato patch(int), por exemplo: 1.0.0, 2018.12.1 etc.*  
**Causa**: O formato válido para uma versão de imagem é três inteiros separados por um período. O nome da versão de imagem não correspondeu ao formato válido.  
**Solução alternativa**: Utilize um nome de versão de imagem que segue o formato Major(int). Menor(int). Patch(int). Por exemplo: 1.0.0. ou 2018.12.1.

**Mensagem**: *O valor da galeria de parâmetrosArtifactVersion.properties.publishingProfile.targetRegions.encryption.dataDiskImages.diskEncryptionSetId é inválido*  
**Causa**: O ID de recursos do conjunto de encriptação do disco utilizado numa imagem de disco de dados utiliza um formato inválido.  
**Solução :** Certifique-se de que o ID de recursos do conjunto de encriptação do disco segue o formato /subscrições/<subscriçãoID \> /resourceGroups/<recursosGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

**Mensagem**: *O valor da galeria de parâmetrosArtifactVersion.properties.publishingProfile.targetRegions.encryption.osDiskImage.diskEncryptionSetId é inválido.*  
**Causa**: O ID de recursos do conjunto de encriptação do disco utilizado na imagem do disco OS utiliza um formato inválido.  
**Solução :** Certifique-se de que o ID de recursos do conjunto de encriptação do disco segue o formato /subscrições/<subscriçãoID \> /resourceGroups/<recursosGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

**Mensagem**: *Não é possível especificar a encriptação de nova imagem de disco de dados lun [número <] com um conjunto de \> encriptação de disco na região [<região ] para atualização do \> pedido de versão de imagem de galeria. Para atualizar esta versão, remova o novo lun. Se precisar de alterar as definições de encriptação de imagem do disco de dados, tem de criar uma nova versão de imagem de galeria com as definições corretas.*  
**Causa:** Adicionou encriptação ao disco de dados de uma versão de imagem existente. Não é possível adicionar encriptação a uma versão de imagem existente.  
**Solução alternativa**: Crie uma nova versão de imagem de galeria ou remova as definições de encriptação adicionadas.

**Mensagem**: *A fonte da versão do artefacto da galeria só pode ser especificada diretamente sob armazenamentoProfile ou dentro de discos individuais de SISTEMA ou de dados. Um e apenas um tipo de origem (imagem do utilizador, instantâneo, disco, máquina virtual) podem ser fornecidos.*  
**Causa:** A identificação da fonte está desaparecida.  
**Resumo :** Certifique-se de que a identificação da fonte está presente.

**Mensagem**: *Fonte não foi encontrada: <recursosID \> . Por favor, certifique-se de que a fonte existe.*  
**Causa**: A identificação do recurso da fonte pode estar incorreta.  
**Resumo :** Certifique-se de que o ID do recurso da fonte está correto.

**Mensagem**: *É necessário um conjunto de encriptação de disco para o disco 'galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.osDiskImage.diskEncryptionSetId' na região-alvo 'região <\_ \> 1' uma vez que o conjunto de encriptação do disco '<diskEncryptionSetID' \> é utilizado para o disco correspondente na região '<região \_ \> 2'*  
**Causa**: A encriptação foi utilizada no disco de osa na região \_ 2 \> <, mas não na região <\_ 1 \> .  
**Solução alternativa**: Se estiver a utilizar encriptação no disco OS, utilize encriptação em todas as regiões.

**Mensagem**: *É necessário um conjunto de encriptação de disco para o disco 'LUN <\> número' na região alvo 'região <1' desde que o \_ conjunto de \> encriptação do disco de disco '<'DiskEncryptionSetID' \> é utilizado para o disco correspondente na região '<região \_ \> 2'*  
**Causa**: A encriptação foi utilizada no disco de dados da LUN <número \> na região <\_ 2 \> , mas não na região 1 <\_ \> .  
**Solução alternativa**: Se estiver a utilizar encriptação num disco de dados, utilize encriptação em todas as regiões.

**Mensagem**: *Um lun inválido [número <\> ] foi especificado em encryption.dataDiskImages. O lun deve ser um dos seguintes valores ['0,9'].*  
**Causa**: O LUN especificado para a encriptação não corresponde a nenhum dos LUNs para discos ligados ao VM.  
**Resumo :** Altere o LUN na encriptação para o LUN de um disco de dados presente no VM.

**Mensagem**: *Duplicado luns '<número \> ' foram especificados na região-alvo '<região \> ' encriptação.dataDiskImages.*  
**Causa**: As definições de encriptação utilizadas na região <\> especificaram um LUN pelo menos duas vezes.  
**Solução :** Mude o LUN na região <\> para garantir que todos os LUNs são únicos na região \> <.

**Mensagem**: *OSDiskImage e DataDiskImage não podem apontar \> para a mesma bolha <fonteID*  
**Causa**: As fontes para o disco so e pelo menos um disco de dados não são únicas.  
**Resolução :** Altere a fonte para o disco de so e/ou discos de dados para garantir que o disco DE, bem como cada disco de dados, é único.

**Mensagem**: *As regiões duplicadas não são permitidas nas regiões editoriais-alvo.*  
**Causa**: Uma região está listada entre as regiões editoriais mais de uma vez.  
**Solução alternativa**: Retire a região duplicada.

**Mensagem**: *Não é permitido adicionar novos discos de dados ou alterar o LUN de um Disco de Dados numa Imagem existente.*  
**Causa**: Uma chamada de atualização para a versão de imagem contém um novo disco de dados ou tem um novo LUN para um disco.  
**Resumo :** Utilize os LUNs e os discos de dados da versão de imagem existente.

**Mensagem**: *O conjunto de encriptação do disco <discoEncryptionSetID \> deve estar na mesma subscrição <subscriçãoID \> que o recurso da galeria.*  
**Causa**: A galeria de imagens partilhada não suporta atualmente a utilização de uma encriptação de disco definida numa subscrição diferente.  
**Solução alternativa**: Crie a versão de imagem e a encriptação do disco definidas na mesma subscrição.

**Mensagem**: *A replicação falhou nesta região devido ao "O tamanho do recurso de fonte galleryImageVersion 2048 excede o tamanho máximo suportado por 1024."*  
**Causa:** Um disco de dados na fonte é superior a 1TB.  
**Solução alternativa**: Redimensione o disco de dados para menos de 1 TB.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Criar ou atualizar um VM ou conjuntos de escala a partir de uma versão de imagem ##

**Mensagem**: *Não existe versão de imagem mais recente para "<imagemDefinitionResourceID" \>*  
**Causa**: A definição de imagem utilizada para implantar a máquina virtual não contém quaisquer versões de imagem que estejam incluídas nas últimas.  
**Solução alternativa**: Certifique-se de que existe pelo menos uma versão de imagem que tenha "Excluir do mais recente" definido para Falso. 

**Mensagem**: *O cliente tem permissão para realizar ações 'Microsoft.Compute/galleries/images/versions/read' no âmbito <recursosID \> , no entanto o atual inquilino <inquilinoID \> não está autorizado a aceder a subscrição ligada <subscriçãoID \> .*  
**Causa**: A máquina virtual ou conjunto de escala foi criado através de uma imagem SIG em outro inquilino. Tentou fazer uma alteração na máquina virtual ou no conjunto de escala, mas não tem acesso à subscrição que detém a imagem.  
**Solução alternativa**: Contacte o proprietário da subscrição da versão de imagem para conceder acesso à versão de imagem.

**Mensagem**: *A imagem da galeria <recursosID não está \> disponível na região <\> região. Entre em contato com o proprietário da imagem para replicar esta região, ou altere a região solicitada.*  
**Causa**: O VM está a ser criado numa região que não está entre a lista de regiões publicadas para a imagem da galeria.  
**Resumo :** Ou replica a imagem para a região ou cria um VM numa das regiões das regiões editoriais da galeria.

**Mensagem**: *Não é permitido o parâmetro 'osProfile'.*  
**Causa**: O nome de utilizador, a palavra-passe ou as teclas SSH foram fornecidas para um VM que foi criado a partir de uma versão de imagem especializada.  
**Solução alternativa**: Não inclua o nome de utilizador, palavra-passe ou SSH se pretender criar um VM a partir dessa imagem. Caso contrário, utilize uma versão de imagem generalizada e forneça o nome de utilizador, palavra-passe ou sSH a administração.

**Mensagem**: *Falta o parâmetro obrigatório 'osProfile' (nulo).*  
**Causa**: O VM é criado a partir de uma imagem generalizada, e falta-lhe o nome de utilizador, palavra-passe ou ssh de administração. Como as imagens generalizadas não retêm o nome de utilizador, palavra-passe ou teclas SSH, estes campos devem ser especificados durante a criação de um conjunto de VM ou escala.  
**Solução :** Especifique o nome de utilizador, palavra-passe ou SSH ou utilize uma versão de imagem especializada.

**Mensagem**: *Não é possível criar a Versão imagem da Galeria a partir de: <recursosID \> uma vez que o Estado OS na imagem da galeria-mãe ('Specialized') não é 'generalizado'.*  
**Causa**: A versão de imagem é criada a partir de uma fonte generalizada, mas a sua definição de pai é especializada.  
**Solução alternativa**: Ou crie a versão de imagem utilizando uma fonte especializada ou utilize uma definição de pai que seja generalizada.

**Mensagem**: Não é possível atualizar o *conjunto de escala de máquina virtual <vmssName como o estado atual do SISTEMA do Conjunto de Escala \> VM é generalizado, o que é diferente do estado de imagem de imagem de galeria atualizado que é Especializado.*  
**Causa**: A imagem de origem atual para o conjunto de escala é uma imagem de origem generalizada, mas está a ser atualizada com uma imagem de origem especializada. A imagem de origem atual e a nova imagem de origem para um conjunto de escala devem ser do mesmo estado.  
**Solução alternativa**: Para atualizar o conjunto de escalas, utilize uma versão de imagem generalizada.

**Mensagem**: *Conjunto de encriptação de disco <diskEncryptionSetID \> na galeria de imagens partilhada <versãoID \> pertence à subscrição <subscriçãoID \_ 1 e não pode ser \> usado com recursos '' na subscrição <subscriçãoID \_ 2 \>*  
**Causa**: O conjunto de encriptação do disco utilizado para encriptar a versão de imagem reside numa subscrição diferente da subscrição para hospedar a versão de imagem.  
**Solução alternativa**: Utilize a mesma subscrição para a versão de imagem e conjunto de encriptação do disco.

**Mensagem**: *A criação de conjunto de escala de VM ou de máquina virtual demora muito tempo.*  
**Solução alternativa**: Verifique se o **OSType** da versão de imagem que está a tentar criar o conjunto de escala de VM ou de máquina virtual tem o mesmo **OSType** da fonte que usou para criar a versão de imagem. 

**Mensagem**: *O recurso com id <vmID \> tem um plano diferente [', \" \" \" <name> \" \" nome: , editor : \" , produto : \" <publisher> \" \" , \" \" <product> \" \" promoção Código : \" \" <promotionCode> \" }'] do que o plano de imagem da galeria dos pais ['nulo'].*  
**Causa**: A definição de imagem dos pais para a versão de imagem que está a ser implementada não tem uma informação do plano de compra.  
**Solução alternativa**: Crie uma definição de imagem com os mesmos detalhes do plano de compra a partir da mensagem de erro e crie a versão de imagem dentro da definição de imagem.


## <a name="creating-a-disk-from-an-image-version"></a>Criar um disco a partir de uma versão de imagem ##

**Mensagem**: *O valor da imagem do parâmetroReferência é inválido.*  
**Causa**: Tentou exportar de uma versão SIG Image para um disco, mas usou uma posição LUN que não existe na imagem.    
**Solução alternativa**: Verifique a versão de imagem para ver quais as posições LUN que estão a ser utilizadas.

## <a name="sharing-resources"></a>Partilha de recursos

A partilha de recursos de galeria de imagens, definição de imagem e versão de imagem através de subscrições é ativada através do [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md). 

## <a name="replication-speed"></a>Velocidade de replicação

Utilize a bandeira **replicationStatus** de expansão para verificar se a replicação de todas as regiões-alvo especificadas terminou. Se não, espere até seis horas para o trabalho terminar. Se falhar, desencadeie novamente o comando para criar e replicar a versão de imagem. Se há muitas regiões-alvo a que a versão de imagem está a ser replicada, considere fazer a replicação por fases.

## <a name="azure-limits-and-quotas"></a>Limites e quotas do Azure 

[Limites e quotas azure](../azure-resource-manager/management/azure-subscription-service-limits.md) aplicam-se a todos os recursos de galeria de imagens partilhadas, definição de imagem e versão de imagem. Certifique-se de que está dentro dos limites das suas subscrições. 


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [galerias de imagens partilhadas.](./shared-image-galleries.md)