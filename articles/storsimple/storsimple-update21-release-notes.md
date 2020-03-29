---
title: StorSimple 8000 Series Update 2.2 notas de lançamento / Microsoft Docs
description: Descreve as novas funcionalidades, problemas e soluções para storSimple 8000 Series Update 2.2.
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
ms.openlocfilehash: 12d11cddf077d4d07732490255d44e89ddaf3217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60531066"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Notas de lançamento da Série StorSimple 8000 Update 2.2

## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões críticas em aberto para o StorSimple 8000 Series Update 2.2. Também contêm uma lista das atualizações de software StorSimple incluídas nesta versão.

A atualização 2.2 pode ser aplicada a qualquer dispositivo StorSimple que execute o Lançamento (GA) ou o Update 0.1 através do Update 2.1. A versão do dispositivo associada ao Update 2.2 é 6.3.9600.17708.

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A atualização 2.2 tem apenas atualizações de software. Demora aproximadamente 1,5 a 2 horas para instalar esta atualização. 
> * Se estiver a executar a Atualização 2.1, recomendamos que aplique o Update 2.2 o mais rapidamente possível.
> * Para novos lançamentos, pode não ver as atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente as atualizações, uma vez que estas estarão disponíveis em breve.
> 
> 

## <a name="whats-new-in-update-22"></a>Novidades na Atualização 2.2
As seguintes melhorias importantes foram introduzidas no Update 2.2.

* **Otimização automatizada** da recuperação do espaço – Quando os dados são eliminados em volumes pouco provisionados, os blocos de armazenamento não utilizados precisam de ser recuperados. Esta versão melhorou o processo de recuperação do espaço a partir da nuvem, resultando em que o espaço não utilizado se tornasse mais rápido em comparação com as versões anteriores.
* **Melhorias** de desempenho instantâneas – A Atualização 2.2 melhorou o tempo para processar uma imagem em nuvem em certos cenários onde grandes volumes estão a ser utilizados e não há qualquer volume de dados. Um cenário que beneficiaria desta melhoria seriam os volumes de arquivo.
* **Endurecimento da recolha de pacotes de apoio** – Houve melhorias na forma como o pacote de suporte é recolhido e carregado nesta versão. 
* **Melhorias** de fiabilidade da atualização – Esta versão tem correções de bugs que resultam numa melhor fiabilidade da Atualização.

## <a name="issues-fixed-in-update-22"></a>Problemas corrigidos na Atualização 2.2
As tabelas seguintes fornecem um resumo das questões que foram corrigidas nas Atualizações 2.2 e 2.1.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivovirtual |
| --- | --- | --- | --- | --- |
| 1 |Desempenho do anfitrião |No lançamento anterior, foram observadas questões de desempenho do lado do hospedeiro durante a criação de um volume fixado localmente e durante a conversão de um volume hierárquico para um volume fixado localmente. Estas questões são fixadas nesta versão, resultando assim numa melhoria do desempenho do hospedeiro durante os procedimentos de criação e conversão de volume. |Sim |Não |
| 2 |volumes afixados localmente |Em casos raros, o sistema falharia ao criar um volume localmente fixado. Este bug foi corrigido nesta libertação. |Sim |Não |
| 3 |Tiering |Houve falhas esporádicas quando os metadados dos Eletrodomésticos StorSimple Cloud (8010 e 8020) foram para a nuvem. Esta questão está corrigida nesta versão. |Não |Sim |
| 4 |Criação instantânea |Houve questões relacionadas com a criação de instantâneos incrementais em cenários com grandes volumes e mínimos a nenhum volume de dados. Estas questões são corrigidas nesta versão. |Sim |Sim |
| 5 |Autenticação openstack |Ao utilizar o Openstack como fornecedor de serviços na nuvem, o utilizador depara-se com um bug pouco frequente relacionado com a autenticação em que o parser JSON resultou num acidente. Este bug está fixado nesta libertação. |Sim |Não |
| 6 |Cópia do lado do anfitrião |Em versões anteriores do software, um bug pouco frequente relacionado com o timing ODX foi visto ao copiar os dados de um volume para outro volume. Isto resultaria numa falha do controlador e o sistema poderia potencialmente entrar no modo recovery. Este bug está fixado nesta libertação. |Sim |Não |
| 7 |Instrumentação de Gestão de Janelas (OMI) |Nas versões anteriores do software, houve vários casos\<de falha de procuração web com exceção " ManagementException> Provider falha de carga". Este bug foi atribuído a uma fuga de memória wmi e agora é corrigido. |Sim |Não |
| 8 |Atualizar |Em certos casos raros, nas versões anteriores do software, o utilizador recebeu um "Erro CisPowershellHcsscript" ao tentar digitalizar ou instalar atualizações. Esta questão está corrigida nesta versão. |Sim |Sim |
| 9 |Pacote de apoio |Nesta versão, houve melhorias na forma como o pacote de suporte é recolhido e carregado. |Sim |Sim |

## <a name="known-issues-in-update-22"></a>Questões conhecidas na Atualização 2.2
A tabela que se segue apresenta um resumo das questões conhecidas nesta versão.

| Não. | Funcionalidade | Problema | Comentários / suposições | Aplica-se ao dispositivo físico | Aplica-se a dispositivovirtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum do disco |Em casos raros, se a maioria dos discos no recinto do EBOD de um dispositivo 8600 for desligado, resultando em nenhum quórum de disco, então a piscina de armazenamento ficará offline. Permanecerá offline mesmo que os discos estejam reconectados. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte o Microsoft Support para os próximos passos. |Sim |Não |
| 2 |ID do controlador incorreto |Quando uma substituição do controlador é efetuada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de pares, o ID do controlador pode aparecer inicialmente como id do controlador de pares. Em casos raros, este comportamento também pode ser visto após um reboot do sistema. |Não é necessária qualquer ação do utilizador. Esta situação resolver-se-á após a substituição do controlador estar completa. |Sim |Não |
| 3 |Contas de armazenamento |A utilização do serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto conduzirá a uma situação em que os dados dos utilizadores não podem ser recuperados. | |Sim |Sim |
| 4 |Falha no dispositivo |Não são suportadas múltiplas falhas de um contentor de volume do mesmo dispositivo de origem para diferentes dispositivos-alvo. A falha de um único dispositivo morto para vários dispositivos fará com que os contentores de volume do primeiro falhado sobre o dispositivo percam a propriedade de dados. Após tal falha, estes contentores de volume aparecerão ou comportar-se-ão de forma diferente quando os vires no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante o Adaptador StorSimple para a instalação do SharePoint, é necessário fornecer um IP do dispositivo para que a instalação termine com sucesso. | |Sim |Não |
| 6 |Proxy Web |Se a configuração do seu proxy web tiver HTTPS como protocolo especificado, então a sua comunicação dispositivo-a-serviço será afetada e o dispositivo ficará offline. Os pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo. |Certifique-se de que o URL de procuração web tem HTTP como protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-configure-web-proxy.md). |Sim |Não |
| 7 |Proxy Web |Se configurar e ativar o proxy web num dispositivo registado, terá de reiniciar o controlador ativo no seu dispositivo. | |Sim |Não |
| 8 |Latência de nuvem alta e alta carga de trabalho em I/S |Quando o seu dispositivo StorSimple encontra uma combinação de latenciências de nuvem muito alta (ordem de segundos) e alta carga de trabalho em I/S, os volumes do dispositivo entram em estado degradado e o I/Os pode falhar com um erro "dispositivo não pronto". |Terá de reiniciar manualmente os controladores do dispositivo ou realizar uma falha no dispositivo para se recuperar desta situação. |Sim |Não |
| 9 |Azure PowerShell |Quando utilizar o StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** para selecionar o primeiro objeto para que possa criar um novo objeto **VolumeContainer,** o cmdlet devolve todos os objetos. |Embrulhe o cmdlet em parênteses da seguinte forma: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Sim |Sim |
| 10 |Migração |Quando vários contentores de volume são passados para migração, o ETA para a última cópia de segurança é preciso apenas para o primeiro recipiente de volume. Além disso, a migração paralela começará após a migração das primeiras 4 cópias de segurança no primeiro contentor de volume. |Recomendamos que emigra um recipiente de volume de cada vez. |Sim |Não |
| 11 |Migração |Após a restauração, os volumes não são adicionados à política de backup ou ao grupo de discos virtuais. |Terá de adicionar estes volumes a uma política de backup para criar backups. |Sim |Sim |
| 12 |Migração |Após a migração estar concluída, o dispositivo da série 5000/7000 não deve aceder aos recipientes de dados migrados. |Recomendamos que apague os recipientes de dados migrados após a migração estar completa e comprometida. |Sim |Não |
| 13 |Clone e DR |Um dispositivo StorSimple que executa o Update 1 não pode clonar ou realizar a recuperação de desastres para um dispositivo que executa o software pré-actualização 1. |Terá de atualizar o dispositivo-alvo para atualizar 1 para permitir estas operações |Sim |Sim |
| 14 |Migração |A cópia de segurança de configuração para migração pode falhar num dispositivo da série 5000-7000 quando há grupos de volume sem volumes associados. |Elimine todos os grupos de volume vazios sem volumes associados e, em seguida, tente novamente a cópia de segurança da configuração. |Sim |Não |
| 15 |Cmdlets Azure PowerShell e volumes fixados localmente |Não é possível criar um volume fixado localmente através de cmdlets Azure PowerShell. (Qualquer volume que criar através do Azure PowerShell será nivido.) |Utilize sempre o serviço StorSimple Manager para configurar volumes fixados localmente. |Sim |Não |
| 16 |Espaço disponível para volumes locais fixados |Se eliminar um volume fixado localmente, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço StorSimple Manager atualiza o espaço local disponível aproximadamente a cada hora. |Aguarde uma hora antes de tentar criar o novo volume. |Sim |Não |
| 17 |volumes afixados localmente |O seu trabalho de restauro expõe o backup de instantâneos temporários no Catálogo de Cópias de Segurança, mas apenas durante a duração do trabalho de restauro. Além disso, expõe um grupo de discos virtuais com prefixo **tmpCollection** na página Políticas de **Backup,** mas apenas durante a duração do trabalho de restauro. |Este comportamento pode ocorrer se o seu trabalho de restauro tiver apenas volumes fixados localmente ou uma mistura de volumes fixados localmente e hierárquicos. Se o trabalho de restauro incluir apenas volumes hierárquicos, então este comportamento não ocorrerá. Não é necessária a intervenção do utilizador. |Sim |Não |
| 18 |volumes afixados localmente |Se cancelar um trabalho de restauro e um reprovação ocorrer imediatamente a seguir, o trabalho de restauro mostrará **falhado** em vez de **cancelado**. Se um trabalho de restauro falhar e um controlador falhar imediatamente depois, o trabalho de restauro mostrará **cancelado** em vez de **Falhado**. |Este comportamento pode ocorrer se o seu trabalho de restauro tiver apenas volumes fixados localmente ou uma mistura de volumes fixados localmente e hierárquicos. Se o trabalho de restauro incluir apenas volumes hierárquicos, então este comportamento não ocorrerá. Não é necessária a intervenção do utilizador. |Sim |Não |
| 19 |volumes afixados localmente |Se cancelar um trabalho de restauro ou se um restauro falhar e, em seguida, ocorrer uma falha no controlador, aparece um trabalho adicional de restauro na página **Jobs.** |Este comportamento pode ocorrer se o seu trabalho de restauro tiver apenas volumes fixados localmente ou uma mistura de volumes fixados localmente e hierárquicos. Se o trabalho de restauro incluir apenas volumes hierárquicos, então este comportamento não ocorrerá. Não é necessária a intervenção do utilizador. |Sim |Não |
| 20 |volumes afixados localmente |Se tentar converter um volume hierárquico (criado e clonado com a Atualização 1.2 ou anterior) para um volume fixado localmente e o seu dispositivo estiver a ficar sem espaço ou se houver uma falha na nuvem, então o clone(s) pode ser corrompido. |Este problema ocorre apenas com volumes que foram criados e clonados com software pré-Actualização 2.1. Este deve ser um cenário pouco frequente. | | |
| 21 |Conversão de volume |Não atualize os ACRs ligados a um volume enquanto estiver em curso uma conversão de volume (nivida a fixação local ou vice-versa). A atualização dos ACRs pode resultar em corrupção de dados. |Se necessário, atualize os ACRs antes da conversão do volume e não efaça mais atualizações aCR enquanto a conversão estiver em curso. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Atualizações do controlador e do firmware no Update 2.2
Esta versão tem atualizações apenas de software. No entanto, se estiver a atualizar a partir de uma versão antes do Update 2, terá de instalar as atualizações de firmware de retenção do controlador, Storport, Spaceport e (em alguns casos) no seu dispositivo.

Para obter mais informações sobre como instalar as atualizações do controlador, Storport, Spaceport e disk firmware, consulte a [instalação do Update 2.2](storsimple-install-update-21.md) no seu dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Atualizações de dispositivos virtuais no Update 2.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Serão necessários novos dispositivos virtuais. 

## <a name="next-step"></a>Passo seguinte
Saiba como instalar o [Update 2.2](storsimple-install-update-21.md) no seu dispositivo StorSimple.

