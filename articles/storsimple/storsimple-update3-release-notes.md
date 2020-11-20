---
title: Notas de lançamento StorSimple 8000 Series Update 3
description: Descreve as novas funcionalidades, problemas e soluções alternativas para StorSimple 8000 Series Update 3.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e081acc357c1b8872dd3a25c552d4cbe9785e212
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956590"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Atualizar 3 notas de lançamento para o seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões abertas críticas para storSimple 8000 Series Update 3. Também contêm uma lista das atualizações de software StorSimple incluídas nesta versão. 

A atualização 3 pode ser aplicada a qualquer dispositivo StorSimple que esteja a executar o Release (GA) ou a Atualização 0.1 através da Atualização 2.2. A versão do dispositivo associada ao Update 3 é de 6.3.9600.17759.

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A Atualização 3 tem software de dispositivos, controlador LSI e firmware, e atualizações Storport e Spaceport. Demora aproximadamente 1,5 a 2 horas a instalar esta atualização. 
> * Para novas versões, pode não ver atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente para atualizações, uma vez que estas ficarão disponíveis em breve.
> 
> 

## <a name="whats-new-in-update-3"></a>Novidades na Atualização 3
As seguintes melhorias chave e correções de erros foram feitas no Update 3.

* **Alterações automatizadas** de recuperação de espaço – A partir da Atualização 3, os algoritmos de recuperação de espaço são executados no controlador de espera do sistema, resultando numa execução mais rápida. Para obter mais informações sobre as portas que são obrigadas a trabalhar com a recuperação do espaço, consulte os [requisitos de rede StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Melhorias de desempenho** – A atualização 3 melhorou o desempenho da leitura-escrita para a nuvem.
* **Melhorias relacionadas com a migração** – Nesta versão, foram feitas várias correções e melhorias para a funcionalidade Migração de dispositivos da série 5000/7000 para 8000 dispositivos da série. Para obter mais informações sobre como utilizar a funcionalidade de migração, vá para [a Migração do dispositivo da série 5000/7000 para o dispositivo da série 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorização de correções relacionadas** - Nesta versão, foram corrigidos os erros relacionados com gráficos de monitorização, painel de serviço e painel de instrumentos.

## <a name="issues-fixed-in-update-3"></a>Problemas corrigidos na Atualização 3
As tabelas que se seguem fornecem um resumo das questões que foram corrigidas na Atualização 3.    

| No | Destaque | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Migração de dados do lado do anfitrião |No lançamento anterior, o StorSimple Cloud Appliance estava desligado durante uma migração de dados do lado do hospedeiro. Esta questão é corrigida nesta versão. |No |Yes |
| 2 |volumes afixados localmente |No lançamento anterior, houve questões relacionadas com falhas de E/S, falhas de conversão de volume e falhas no datapath para volumes fixados localmente. Estas questões foram causadas por raízes e corrigidas nesta versão. |Yes |No |
| 3 |Monitorização |Houve vários problemas relacionados com unidades de reporte e monitorização, bem como gráficos de dashboard de dispositivos onde foram apresentadas informações incorretas para volumes fixados localmente. Estas questões são corrigidas nesta versão. |Yes |No |
| 4 |Pesado escreve I/O |Ao utilizar o StorSimple para cargas de trabalho que envolvam escritas pesadas, o utilizador desatambria um bug pouco frequente onde o conjunto de trabalho estava a ser colocado na nuvem. Este bug está fixo nesta versão. |Yes |Yes |
| 5 |Backup |Em certos casos raros, nas versões anteriores do software, quando o utilizador fez uma cópia de segurança de um clone remoto, eles iriam encontrar erros na nuvem e a operação seria errada. Nesta versão, o problema é corrigido e a operação completa-se com sucesso. |Yes |Yes |
| 6 |Política de cópias de segurança |Em certos casos raros, nas anteriores versões de software, houve um bug relacionado com a eliminação da política de backup. Esta questão é corrigida nesta versão. |Yes |Yes |

## <a name="known-issues-in-update-3"></a>Problemas conhecidos na Atualização 3
A tabela seguinte fornece um resumo de questões conhecidas nesta versão.

| Não. | Funcionalidade | Problema | Comentários / solução alternativa | Aplica-se ao dispositivo físico | Aplica-se a dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum de disco |Em casos raros, se a maioria dos discos no recinto EBOD de um dispositivo 8600 forem desligados, resultando em nenhum quórum de disco, então a piscina de armazenamento ficará offline. Permanecerá offline mesmo que os discos estejam novamente ligados. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte o Microsoft Support para os próximos passos. |Yes |No |
| 2 |ID do controlador incorreto |Quando uma substituição do controlador é efetuada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de pares, o ID do controlador pode aparecer inicialmente como ID do controlador de pares. Em casos raros, este comportamento também pode ser visto após um reboot do sistema. |Não é necessária qualquer ação do utilizador. Esta situação resolver-se-á após a substituição do controlador. |Yes |No |
| 3 |Contas de armazenamento |A utilização do serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto conduzirá a uma situação em que os dados dos utilizadores não possam ser recuperados. | |Yes |Yes |
| 4 |Falha do dispositivo |Não são suportadas múltiplas falhas de um recipiente de volume do mesmo dispositivo de origem para diferentes dispositivos-alvo. A falha de um único dispositivo morto para vários dispositivos fará com que os recipientes de volume do primeiro dispositivo falhados percam a propriedade dos dados. Após tal falha, estes recipientes de volume aparecerão ou comportar-se-ão de forma diferente quando os vê no portal clássico do Azure. | |Yes |No |
| 5 |Instalação |Durante o Adaptador StorSimple para a instalação do SharePoint, é necessário fornecer um IP do dispositivo para que a instalação termine com sucesso. | |Yes |No |
| 6 |Proxy Web |Se a sua configuração de procuração web tiver HTTPS como o protocolo especificado, então a sua comunicação dispositivo-a-serviço será afetada e o dispositivo ficará offline. Os pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo. |Certifique-se de que o URL de procuração web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-8000-configure-web-proxy.md). |Yes |No |
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
| 22 |Atualizações |Ao aplicar o Update 3, a página **manutenção** no portal clássico Azure apresentará a seguinte mensagem relacionada com o Update 2 - "StorSimple série Update 2 inclui a capacidade da Microsoft de recolher proativamente informações de registo do seu dispositivo quando detetarmos potenciais problemas". Isto é enganador, uma vez que indica que o dispositivo está a ser atualizado para o Update 2. Depois de o dispositivo ser atualizado com sucesso para o Update 3, esta mensagem desaparecerá. |Este comportamento será corrigido numa futura libertação. |Yes |No |

## <a name="controller-and-firmware-updates-in-update-3"></a>Atualizações de controlador e firmware na Atualização 3
Esta versão tem atualizações de controlador e firmware LSI. Para obter mais informações sobre como instalar as atualizações do controlador LSI e do firmware, consulte [a instalação Update 3](./index.yml) no seu dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Atualizações de dispositivos virtuais no Update 3
Esta atualização não pode ser aplicada ao StorSimple Cloud Appliance (também conhecido como dispositivo virtual). Novos dispositivos virtuais terão de ser criados. 

## <a name="next-step"></a>Passo seguinte
Saiba como instalar o [Update 3](./index.yml) no seu dispositivo StorSimple.