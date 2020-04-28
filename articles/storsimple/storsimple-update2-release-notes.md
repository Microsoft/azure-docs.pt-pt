---
title: Notas de lançamento da Série 2 storSimple 8000 Microsoft Docs
description: Descreve as novas funcionalidades, problemas e soluções para storSimple 8000 Series Update 2.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: 4e57fffd2f74ae1b14f51537c92299607f193ad5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75934063"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Notas de lançamento da Série 2 storSimple 8000

## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões críticas em aberto para o StorSimple 8000 Series Update 2. Também contêm uma lista das atualizações de software, controlador e firmware do storSimple incluídas nesta versão. 

A atualização 2 pode ser aplicada a qualquer dispositivo StorSimple que execute o Lançamento (GA) ou o Update 0.1 através do Update 1.2. A versão do dispositivo associada ao Update 2 é de 6.3.9600.17673.

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * Demora aproximadamente 4 a 7 horas a instalar esta atualização (incluindo as atualizações do Windows). 
> * A Atualização 2 tem software, controlador LSI e atualizações de firmware SSD.
> * Para novos lançamentos, pode não ver as atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente as atualizações, uma vez que estas estarão disponíveis em breve.
> 
> 

## <a name="whats-new-in-update-2"></a>Novidades na Atualização 2
A atualização 2 introduz as seguintes novas funcionalidades.

* **Volumes fixados localmente** – Em lançamentos anteriores da série StorSimple 8000, os blocos de dados foram nientes para a nuvem com base no uso. Não havia como garantir que os blocos ficariam no local. No Update 2, quando criar um volume, pode designar um volume como fixado localmente, e os dados primários desse volume não serão nividados para a nuvem. Os instantâneos de volumes fixados localmente ainda serão copiados para a nuvem para backup para que a nuvem possa ser usada para fins de mobilidade de dados e recuperação de desastres. Além disso, pode alterar o tipo de volume (isto é, converter volumes hierárquicos em volumes fixados localmente e converter volumes fixados localmente em camadas). 
* **Melhorias do dispositivo virtual StorSimple** – Anteriormente, a série StorSimple 8000 posicionou o dispositivo virtual como uma solução de recuperação de desastres ou desenvolvimento/teste. Havia apenas um modelo de dispositivo virtual (modelo 1100). A atualização 2 introduz dois modelos de dispositivos virtuais: 
  
  * 8010 (anteriormente chamado de 1100) – Sem alterações; tem uma capacidade de 30 TB e utiliza armazenamento padrão Azure.
  * 8020 – Tem uma capacidade de 64 TB e utiliza o armazenamento Azure Premium para um melhor desempenho.
    
    Existe um único VHD para ambos os modelos de dispositivos virtuais (8010/8020). Quando inicia o dispositivo virtual, deteta os parâmetros da plataforma e aplica a versão correta do modelo.
* **Melhorias de Rede** – A Atualização 2 contém as seguintes melhorias de rede:
  
  * Vários NICs podem ser ativados para a nuvem para que a falha possa ocorrer se um NIC falhar.
  * Melhorias de encaminhamento, com métricas fixas para blocos ativados pela nuvem.
  * Repetição online de recursos falhados antes de uma falha.
  * Novos alertas para falhas de serviço.
* **Atualizando melhorias** – Na Atualização 1.2 e anterior, a série StorSimple 8000 foi atualizada através de dois canais: Windows Update para clustering, iSCSI, e assim por diante, e Microsoft Update para binários e firmware.
    O Update 2 utiliza o Microsoft Update para todos os pacotes de atualização. Isto deve levar a menos tempo a remendar ou a fazer falhas. 
* **Atualizações de Firmware** – As seguintes atualizações de firmware estão incluídas:
  
  * LSI: versão do produto lsi_sas2.sys versão 2.00.72.10
  * SSD apenas (sem atualizações HDD): XMGG, XGEG, KZ50, F6C2 e VR08
* **Suporte Proactivo** – O Update 2 permite à Microsoft retirar informações adicionais de diagnóstico do dispositivo. Quando a nossa equipa de operações identifica dispositivos que estão com problemas, estamos mais bem equipados para recolher informações do dispositivo e diagnosticar problemas. **Ao aceitar a Atualização 2, permite-nos fornecer este suporte proactivo**.    

## <a name="issues-fixed-in-update-2"></a>Problemas corrigidos na Atualização 2
As tabelas seguintes fornecem um resumo das questões que foram corrigidas nas Atualizações 2.    

| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivovirtual |
| --- | --- | --- | --- | --- |
| 1 |Interfaces de rede |Após uma atualização para a Atualização 1, o serviço StorSimple Manager informou que as portas Data2 e Data3 falharam num controlador. Esta questão foi corrigida. |Sim |Não |
| 2 |Atualizações |Após uma atualização para o Update 1, ocorreram alertas de alarme sonoros no portal clássico azure em vários dispositivos. Esta questão foi corrigida. |Sim |Não |
| 3 |Autenticação openstack |Ao utilizar o Openstack como fornecedor de serviços na nuvem, pode receber um erro de que a sua cadeia de autenticação em nuvem era demasiado longa. Este problema foi corrigido. |Sim |Não |

## <a name="known-issues-in-update-2"></a>Questões conhecidas na Atualização 2
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
| 20 |volumes afixados localmente |Se tentar converter um volume hierárquico (criado e clonado com a Atualização 1.2 ou anterior) para um volume fixado localmente e o seu dispositivo estiver a ficar sem espaço ou se houver uma falha na nuvem, então o clone(s) pode ser corrompido. |Este problema ocorre apenas com volumes que foram criados e clonados com software pré-Actualização 2. Este deve ser um cenário pouco frequente. | | |
| 21 |Conversão de volume |Não atualize os ACRs ligados a um volume enquanto estiver em curso uma conversão de volume (nivida a fixação local ou vice-versa). A atualização dos ACRs pode resultar em corrupção de dados. |Se necessário, atualize os ACRs antes da conversão do volume e não efaça mais atualizações aCR enquanto a conversão estiver em curso. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Atualizações de controlador e firmware no Update 2
Esta versão atualiza o controlador e o firmware do disco no seu dispositivo.

* Para obter mais informações sobre a atualização de firmware LSI, consulte o artigo base do Microsoft Knowledge 3121900. 
* Para obter mais informações sobre a atualização do firmware do disco, consulte o artigo base do Microsoft Knowledge 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Atualizações de dispositivos virtuais no Update 2
Esta atualização não pode ser aplicada ao dispositivo virtual. Serão necessários novos dispositivos virtuais. 

## <a name="next-step"></a>Passo seguinte
Saiba como instalar o [Update 2](storsimple-install-update-2.md) no seu dispositivo StorSimple.

