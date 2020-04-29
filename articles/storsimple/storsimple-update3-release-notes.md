---
title: Notas de lançamento da Série 3 StorSimple 8000
description: Descreve as novas funcionalidades, problemas e soluções para storSimple 8000 Series Update 3.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b61caecd67881eb08c82ea0c26522c63c3e8396
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79254615"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Atualização 3 notas de lançamento para o seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões críticas em aberto para o StorSimple 8000 Series Update 3. Também contêm uma lista das atualizações de software StorSimple incluídas nesta versão. 

A atualização 3 pode ser aplicada a qualquer dispositivo StorSimple que execute o Lançamento (GA) ou o Update 0.1 através do Update 2.2. A versão do dispositivo associada ao Update 3 é de 6.3.9600.17759.

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A Atualização 3 tem software de dispositivos, controlador LSI e firmware, e atualizações de Storport e Spaceport. Demora aproximadamente 1,5 a 2 horas para instalar esta atualização. 
> * Para novos lançamentos, pode não ver as atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente as atualizações, uma vez que estas estarão disponíveis em breve.
> 
> 

## <a name="whats-new-in-update-3"></a>Novidades na Atualização 3
As seguintes melhorias de teclas e correções de erros foram feitas no Update 3.

* Mudanças de recuperação de **espaço automatizadas** – StartUpdate 3, os algoritmos de recuperação de espaço funcionam no controlador de espera do sistema resultando numa execução mais rápida. Para obter mais informações sobre as portas que são necessárias para trabalhar com a recuperação do espaço, consulte os requisitos de [rede StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Melhorias** de desempenho – A Atualização 3 melhorou o desempenho da leitura-escrita para a nuvem.
* **Melhorias relacionadas com** a migração – Nesta versão, foram feitas várias correções e melhorias de bugs para a funcionalidade Migração de dispositivos da série 5000/7000 para dispositivos da série 8000. Para obter mais informações sobre como utilizar a funcionalidade de migração, vá para a [Migração de 5000/7000 dispositivo da série para dispositivo da série 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Sistemas relacionados** de monitorização - Nesta versão, foram corrigidos os bugs relacionados com as tabelas de monitorização, o painel de assistência e o painel de instrumentos do dispositivo.

## <a name="issues-fixed-in-update-3"></a>Problemas corrigidos na Atualização 3
As tabelas seguintes fornecem um resumo das questões que foram corrigidas na Atualização 3.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivovirtual |
| --- | --- | --- | --- | --- |
| 1 |Migração de dados do lado do hospedeiro |No lançamento anterior, o StorSimple Cloud Appliance estava desligado durante uma migração de dados do lado do hospedeiro. Esta questão está corrigida nesta versão. |Não |Sim |
| 2 |volumes afixados localmente |Na versão anterior, houve problemas relacionados com falhas de I/O, falhas de conversão de volume e falhas na trajetória de dados para volumes fixados localmente. Estas questões foram causadas e corrigidas nesta libertação. |Sim |Não |
| 3 |Monitorização |Houve vários problemas relacionados com unidades de reporte e monitorização, bem como gráficos de painel de instrumentos de dispositivos onde foram exibidas informações incorretas para volumes fixados localmente. Estas questões são corrigidas nesta versão. |Sim |Não |
| 4 |Heavy escreve I/O |Ao utilizar o StorSimple para cargas de trabalho que envolvam escritas pesadas, o utilizador encontrava um bug pouco frequente onde o conjunto de trabalho estava a ser nivida para a nuvem. Este bug está fixado nesta libertação. |Sim |Sim |
| 5 |Cópia de segurança |Em certos casos raros, nas versões anteriores do software, quando o utilizador recebeu uma cópia de segurança de um clone remoto, eles iriam encontrar erros na nuvem e a operação seria errada. Nesta versão, a questão é corrigida e a operação completa com sucesso. |Sim |Sim |
| 6 |Política de cópia de segurança |Em certos casos raros, nos lançamentos anteriores do software, houve um bug relacionado com a eliminação da política de backup. Esta questão está corrigida nesta versão. |Sim |Sim |

## <a name="known-issues-in-update-3"></a>Questões conhecidas na Atualização 3
A tabela que se segue apresenta um resumo das questões conhecidas nesta versão.

| Não. | Funcionalidade | Problema | Comentários / suposições | Aplica-se ao dispositivo físico | Aplica-se a dispositivovirtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum do disco |Em casos raros, se a maioria dos discos no recinto do EBOD de um dispositivo 8600 for desligado, resultando em nenhum quórum de disco, então a piscina de armazenamento ficará offline. Permanecerá offline mesmo que os discos estejam reconectados. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte o Microsoft Support para os próximos passos. |Sim |Não |
| 2 |ID do controlador incorreto |Quando uma substituição do controlador é efetuada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de pares, o ID do controlador pode aparecer inicialmente como id do controlador de pares. Em casos raros, este comportamento também pode ser visto após um reboot do sistema. |Não é necessária qualquer ação do utilizador. Esta situação resolver-se-á após a substituição do controlador estar completa. |Sim |Não |
| 3 |Contas de armazenamento |A utilização do serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto conduzirá a uma situação em que os dados dos utilizadores não podem ser recuperados. | |Sim |Sim |
| 4 |Falha no dispositivo |Não são suportadas múltiplas falhas de um contentor de volume do mesmo dispositivo de origem para diferentes dispositivos-alvo. A falha de um único dispositivo morto para vários dispositivos fará com que os contentores de volume do primeiro falhado sobre o dispositivo percam a propriedade de dados. Após tal falha, estes contentores de volume aparecerão ou comportar-se-ão de forma diferente quando os vires no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante o Adaptador StorSimple para a instalação do SharePoint, é necessário fornecer um IP do dispositivo para que a instalação termine com sucesso. | |Sim |Não |
| 6 |Proxy Web |Se a configuração do seu proxy web tiver HTTPS como protocolo especificado, então a sua comunicação dispositivo-a-serviço será afetada e o dispositivo ficará offline. Os pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo. |Certifique-se de que o URL de procuração web tem HTTP como protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-8000-configure-web-proxy.md). |Sim |Não |
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
| 22 |Atualizações |Ao aplicar o Update 3, a página **de Manutenção** do portal clássico Azure apresentará a seguinte mensagem relacionada com o Update 2 - "StorSimple 8000 série Update 2 inclui a capacidade de a Microsoft recolher proativamente informações de registo do seu dispositivo quando detetarmos potenciais problemas". Isto é enganador, pois indica que o dispositivo está a ser atualizado para o Update 2. Depois de o dispositivo ser atualizado com sucesso para o Update 3, esta mensagem desaparecerá. |Este comportamento será corrigido numa futura libertação. |Sim |Não |

## <a name="controller-and-firmware-updates-in-update-3"></a>Atualizações de controlador e firmware na Atualização 3
Esta versão tem atualizações de controlador LSI e firmware. Para obter mais informações sobre como instalar as atualizações de controlador lSI e firmware, consulte [a instalação do Update 3](storsimple-install-update-3.md) no seu dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Atualizações de dispositivos virtuais no Update 3
Esta atualização não pode ser aplicada ao StorSimple Cloud Appliance (também conhecido como dispositivo virtual). Serão necessários novos dispositivos virtuais. 

## <a name="next-step"></a>Passo seguinte
Saiba como instalar o [Update 3](storsimple-install-update-3.md) no seu dispositivo StorSimple.

