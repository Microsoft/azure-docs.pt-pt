---
title: StorSimple 8000 Series Update 2.2 notas de lançamento | Microsoft Docs
description: Descreve as novas funcionalidades, problemas e soluções alternativas para StorSimple 8000 Series Update 2.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 73b9ecd03875b60ed2d9b9d4c8e8a3a0c8de3cfa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956607"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 Series Update 2.2 notas de lançamento

## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões abertas críticas para storSimple 8000 Series Update 2.2. Também contêm uma lista das atualizações de software StorSimple incluídas nesta versão.

A atualização 2.2 pode ser aplicada a qualquer dispositivo StorSimple que esteja a executar o Release (GA) ou a Atualização 0.1 através da Atualização 2.1. A versão do dispositivo associada ao Update 2.2 é de 6.3.9600.17708.

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A atualização 2.2 tem apenas atualizações de software. Demora aproximadamente 1,5 a 2 horas a instalar esta atualização. 
> * Se estiver a executar a Atualização 2.1, recomendamos que aplique a Atualização 2.2 o mais rapidamente possível.
> * Para novas versões, pode não ver atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente para atualizações, uma vez que estas ficarão disponíveis em breve.
> 
> 

## <a name="whats-new-in-update-22"></a>Novidades na Atualização 2.2
As seguintes melhorias chave foram feitas no Update 2.2.

* **Otimização automatizada** da recuperação do espaço – Quando os dados são eliminados em volumes pouco aprovisionados, os blocos de armazenamento não reutilizados precisam de ser recuperados. Este lançamento melhorou o processo de recuperação do espaço a partir da nuvem, resultando no espaço não reutilizado a tornar-se mais rapidamente disponível em comparação com as versões anteriores.
* **Melhorias de desempenho instantâneas** – A atualização 2.2 melhorou o tempo para processar uma imagem em nuvem em certos cenários onde estão a ser utilizados grandes volumes e não há dados mínimos. Um cenário que beneficiaria desta melhoria seriam os volumes de arquivo.
* **Endurecimento da recolha de pacotes de apoio** – Houve melhorias na forma como o pacote de Suporte é recolhido e carregado nesta versão. 
* **Atualiza melhorias de fiabilidade** – Esta versão tem correções de bugs que resultam numa melhor fiabilidade da Atualização.

## <a name="issues-fixed-in-update-22"></a>Problemas fixados na Atualização 2.2
As tabelas que se seguem fornecem um resumo das questões que foram corrigidas nas Atualizações 2.2 e 2.1.    

| No | Destaque | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Desempenho do anfitrião |No lançamento anterior, foram observados problemas de desempenho do lado do hospedeiro durante a criação de um volume fixado localmente e durante a conversão de um volume hierárquico para um volume fixado localmente. Estas questões são corrigidas nesta versão, resultando assim numa melhoria do desempenho do hospedeiro durante os procedimentos de criação e conversão de volume. |Yes |No |
| 2 |volumes afixados localmente |Em casos raros, o sistema falharia ao criar um volume fixado localmente. Este bug foi corrigido nesta versão. |Yes |No |
| 3 |Tiering |Houve acidentes esporádicos quando os metadados dos StorSimple Cloud Appliances (8010 e 8020) se dilaceraram para a nuvem. Esta questão é corrigida nesta versão. |No |Yes |
| 4 |Criação instantânea |Havia questões relacionadas com a criação de instantâneos incrementais em cenários com grandes volumes e o mínimo a nenhum dado. Estas questões são corrigidas nesta versão. |Yes |Yes |
| 5 |Autenticação openstack |Ao utilizar o Openstack como fornecedor de serviços de nuvem, o utilizador desabrochou com um bug pouco frequente relacionado com a autenticação onde o parser JSON resultou numa falha. Este bug está fixo nesta versão. |Yes |No |
| 6 |Cópia do lado do anfitrião |Em versões anteriores do software, um bug pouco frequente relacionado com o tempo ODX foi visto ao copiar os dados de um volume para outro volume. Isto resultaria num failover do controlador e o sistema poderia potencialmente entrar no modo Recovery. Este bug está fixo nesta versão. |Yes |No |
| 7 |Instrumentação de gestão do Windows (WMI) |Nas versões anteriores do software, houve vários casos de falha de procuração web com a exceção " \<ManagementException> Falha de carga do fornecedor". Este bug foi atribuído a uma fuga de memória do WMI e está agora corrigido. |Yes |No |
| 8 |Atualizar |Em certos casos raros, nas versões anteriores do software, o utilizador recebeu um "CisPowershellHcsscripterror" ao tentar digitalizar ou instalar atualizações. Esta questão é corrigida nesta versão. |Yes |Yes |
| 9 |Pacote de apoio |Nesta versão, houve melhorias na forma como o pacote de suporte é recolhido e carregado. |Yes |Yes |

## <a name="known-issues-in-update-22"></a>Emissões conhecidas na Atualização 2.2
A tabela seguinte fornece um resumo de questões conhecidas nesta versão.

| N.º | Funcionalidade | Problema | Comentários / solução alternativa | Aplica-se ao dispositivo físico | Aplica-se a dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum de disco |Em casos raros, se a maioria dos discos no recinto EBOD de um dispositivo 8600 forem desligados, resultando em nenhum quórum de disco, então a piscina de armazenamento ficará offline. Permanecerá offline mesmo que os discos estejam novamente ligados. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte o Microsoft Support para os próximos passos. |Yes |No |
| 2 |ID do controlador incorreto |Quando uma substituição do controlador é efetuada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de pares, o ID do controlador pode aparecer inicialmente como ID do controlador de pares. Em casos raros, este comportamento também pode ser visto após um reboot do sistema. |Não é necessária qualquer ação do utilizador. Esta situação resolver-se-á após a substituição do controlador. |Yes |No |
| 3 |Contas de armazenamento |A utilização do serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto conduzirá a uma situação em que os dados dos utilizadores não possam ser recuperados. | |Yes |Yes |
| 4 |Falha do dispositivo |Não são suportadas múltiplas falhas de um recipiente de volume do mesmo dispositivo de origem para diferentes dispositivos-alvo. A falha de um único dispositivo morto para vários dispositivos fará com que os recipientes de volume do primeiro dispositivo falhados percam a propriedade dos dados. Após tal falha, estes recipientes de volume aparecerão ou comportar-se-ão de forma diferente quando os vê no portal clássico do Azure. | |Yes |No |
| 5 |Instalação |Durante o Adaptador StorSimple para a instalação do SharePoint, é necessário fornecer um IP do dispositivo para que a instalação termine com sucesso. | |Yes |No |
| 6 |Proxy Web |Se a sua configuração de procuração web tiver HTTPS como o protocolo especificado, então a sua comunicação dispositivo-a-serviço será afetada e o dispositivo ficará offline. Os pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo. |Certifique-se de que o URL de procuração web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](./storsimple-8000-configure-web-proxy.md). |Yes |No |
| 7 |Proxy Web |Se configurar e ativar o representante web num dispositivo registado, terá de reiniciar o controlador ativo no seu dispositivo. | |Yes |No |
| 8 |Alta latência em nuvem e alta carga de trabalho de E/O |Quando o seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito elevadas (ordem de segundos) e carga de trabalho de E/S elevada, os volumes do dispositivo entram num estado degradado e o I/Os pode falhar com um erro de "dispositivo não pronto". |Terá de reiniciar manualmente os controladores do dispositivo ou executar um dispositivo que falhe para recuperar desta situação. |Yes |No |
| 9 |Azure PowerShell |Quando utilizar o cmdlet StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Aguarde** para selecionar o primeiro objeto para que possa criar um novo objeto **VolumeContainer,** o cmdlet devolve todos os objetos. |Embrulhe o cmdlet em parênteses da seguinte forma: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Yes |Yes |
| 10 |Migração |Quando vários recipientes de volume são passados para migração, o ETA para a cópia de segurança mais recente é preciso apenas para o primeiro recipiente de volume. Além disso, a migração paralela começará após a migração dos primeiros 4 backups do primeiro recipiente de volume. |Recomendamos que emigre um recipiente de volume de cada vez. |Yes |No |
| 11 |Migração |Após a restauração, os volumes não são adicionados à política de backup ou ao grupo de discos virtuais. |Terá de adicionar estes volumes a uma política de backup para criar cópias de segurança. |Yes |Yes |
| 12 |Migração |Após a conclusão da migração, o dispositivo da série 5000/7000 não deve aceder aos recipientes de dados migrados. |Recomendamos que elimine os recipientes de dados migrados após a migração estar completa e comprometida. |Yes |No |
| 13 |Clone e DR |Um dispositivo StorSimple em execução Update 1 não pode clonar ou realizar a recuperação de desastres para um dispositivo que executa o software pré-actualização 1. |Terá de atualizar o dispositivo-alvo para atualizar 1 para permitir estas operações |Yes |Yes |
| 14 |Migração |A cópia de segurança de configuração para migração pode falhar num dispositivo da série 5000-7000 quando existem grupos de volume sem volumes associados. |Elimine todos os grupos de volume vazios sem volumes associados e, em seguida, re-teste a cópia de segurança da configuração. |Yes |No |
| 15 |Cmdlets Azure PowerShell e volumes fixados localmente |Não é possível criar um volume fixado localmente através de cmdlets Azure PowerShell. (Qualquer volume que criar via Azure PowerShell será hierárquico.) |Utilize sempre o serviço StorSimple Manager para configurar volumes fixados localmente. |Yes |No |
| 16 |Espaço disponível para volumes fixados localmente |Se eliminar um volume fixado localmente, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço StorSimple Manager atualiza o espaço local disponível aproximadamente a cada hora. |Espere uma hora antes de tentar criar o novo volume. |Yes |No |
| 17 |volumes afixados localmente |O seu trabalho de restauro expõe a cópia de segurança temporária no Catálogo de Cópias de Segurança, mas apenas durante o período de trabalho de restauro. Além disso, expõe um grupo de discos virtuais com **tmpCollection** pré-fixo na página **Políticas de Backup,** mas apenas durante a duração do trabalho de restauro. |Este comportamento pode ocorrer se o seu trabalho de restauro tiver apenas volumes fixados localmente ou uma mistura de volumes fixados localmente e hierárquicos. Se o trabalho de restauro incluir apenas volumes hierárquicos, então este comportamento não ocorrerá. Não é necessária a intervenção do utilizador. |Yes |No |
| 18 |volumes afixados localmente |Se cancelar um trabalho de restauro e ocorrer um falha no controlador imediatamente a seguir, o trabalho de restauro mostrará **Falha** em vez de **Cancelado**. Se um trabalho de restauro falhar e ocorrer um falha no controlador imediatamente a seguir, o trabalho de restauro mostrará **cancelado** em vez de **Falhado**. |Este comportamento pode ocorrer se o seu trabalho de restauro tiver apenas volumes fixados localmente ou uma mistura de volumes fixados localmente e hierárquicos. Se o trabalho de restauro incluir apenas volumes hierárquicos, então este comportamento não ocorrerá. Não é necessária a intervenção do utilizador. |Yes |No |
| 19 |volumes afixados localmente |Se cancelar uma função de restauro ou se uma restauração falhar e ocorrer um falha no controlador, aparece uma função de restauro adicional na página **Jobs.** |Este comportamento pode ocorrer se o seu trabalho de restauro tiver apenas volumes fixados localmente ou uma mistura de volumes fixados localmente e hierárquicos. Se o trabalho de restauro incluir apenas volumes hierárquicos, então este comportamento não ocorrerá. Não é necessária a intervenção do utilizador. |Yes |No |
| 20 |volumes afixados localmente |Se tentar converter um volume hierárquico (criado e clonado com a Atualização 1.2 ou mais cedo) para um volume fixado localmente e o seu dispositivo estiver a ficar sem espaço ou se houver uma falha na nuvem, então o clone(s) pode ser corrompido. |Este problema ocorre apenas com volumes que foram criados e clonados com software pré-Update 2.1. Este deve ser um cenário pouco frequente. | | |
| 21 |Conversão de volume |Não atualize os ACRs ligados a um volume enquanto estiver em curso uma conversão de volume (tiered to local fixed or vice-versa). A atualização dos ACRs pode resultar em corrupção de dados. |Se necessário, atualize os ACRs antes da conversão do volume e não entede mais atualizações de ACR enquanto a conversão estiver em curso. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Atualizações de controlador e firmware na Atualização 2.2
Esta versão tem apenas atualizações de software. No entanto, se estiver a atualizar a partir de uma versão antes do Update 2, terá de instalar atualizações de firmware de controlador, Storport, Spaceport e (em alguns casos) de firmware de disco no seu dispositivo.

Para obter mais informações sobre como instalar as atualizações do controlador, Storport, Spaceport e firmware de disco, consulte [a instalação Update 2.2](./storsimple-8000-install-update-5.md) no seu dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Atualizações de dispositivos virtuais no Update 2.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais terão de ser criados. 

## <a name="next-step"></a>Passo seguinte
Saiba como [instalar o Update 2.2](./storsimple-8000-install-update-5.md) no seu dispositivo StorSimple.