---
title: StorSimple 8000 Series Update 2 notas de lançamento | Microsoft Docs
description: Descreve as novas funcionalidades, problemas e soluções alternativas para StorSimple 8000 Series Update 2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b68e338c2675a3aeefda390d12ab2d82ea1c7a9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956658"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Notas de lançamento StorSimple 8000 Series Update 2

## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões abertas críticas para storSimple 8000 Series Update 2. Também contêm uma lista das atualizações de software, controlador e firmware de disco StorSimple incluídas nesta versão. 

A atualização 2 pode ser aplicada a qualquer dispositivo StorSimple que esteja a executar o Release (GA) ou a Atualização 0.1 através da Atualização 1.2. A versão do dispositivo associada ao Update 2 é de 6.3.9600.17673.

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * Demora aproximadamente 4 a 7 horas a instalar esta atualização (incluindo as atualizações do Windows). 
> * A Atualização 2 tem atualizações de software, controlador LSI e firmware SSD.
> * Para novas versões, pode não ver atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente para atualizações, uma vez que estas ficarão disponíveis em breve.
> 
> 

## <a name="whats-new-in-update-2"></a>Novidades na Atualização 2
A atualização 2 introduz as seguintes novas funcionalidades.

* **Volumes fixados localmente** – Em lançamentos anteriores da série StorSimple 8000, os blocos de dados foram hierárquicos para a nuvem com base na utilização. Não havia como garantir que os blocos ficariam no local. Na Atualização 2, quando criar um volume, pode designar um volume como fixado localmente, e os dados primários desse volume não serão classificados para a nuvem. As imagens de volumes fixados localmente ainda serão copiadas para a nuvem para obter cópias de segurança para que a nuvem possa ser usada para fins de mobilidade de dados e recuperação de desastres. Além disso, pode alterar o tipo de volume (isto é, converter volumes hierárquicos para volumes fixados localmente e converter volumes fixados localmente para tiered). 
* **Melhorias do dispositivo virtual StorSimple** – Anteriormente, a série StorSimple 8000 posicionava o dispositivo virtual como uma solução de recuperação ou desenvolvimento/teste de desastres. Havia apenas um modelo de dispositivo virtual (modelo 1100). A Atualização 2 introduz dois modelos de dispositivos virtuais: 
  
  * 8010 (anteriormente chamado de 1100) – Sem alteração; tem uma capacidade de 30 TB e utiliza armazenamento padrão Azure.
  * 8020 - Tem uma capacidade de 64 TB e utiliza o armazenamento Azure Premium para um melhor desempenho.
    
    Existe um único VHD para ambos os modelos de dispositivos virtuais (8010/8020). Quando inicia o dispositivo virtual, deteta os parâmetros da plataforma e aplica a versão correta do modelo.
* **Melhorias na rede** – A atualização 2 contém as seguintes melhorias na rede:
  
  * Vários NICs podem ser ativados para a nuvem de modo que a falha pode ocorrer se um NIC falhar.
  * Melhorias no encaminhamento, com métricas fixas para blocos com ativação de nuvens.
  * Relembarcação online de recursos falhados antes de um fracasso.
  * Novos alertas para falhas de serviço.
* **Atualização de Melhorias** – No Update 1.2 e anterior, a série StorSimple 8000 foi atualizada através de dois canais: Windows Update para clustering, iSCSI, e assim por diante, e Microsoft Update para binários e firmware.
    A atualização 2 utiliza o Microsoft Update para todos os pacotes de atualização. Isto deve levar a menos tempo de remendos ou a fazer falhas. 
* **Atualizações do Firmware** – As seguintes atualizações do firmware estão incluídas:
  
  * LSI: lsi_sas2.sys Versão do Produto 2.00.72.10
  * Apenas SSD (sem atualizações HDD): XMGG, XGEG, KZ50, F6C2 e VR08
* **Suporte Proativo** – O Update 2 permite à Microsoft extrair informações adicionais de diagnóstico do dispositivo. Quando a nossa equipa de operações identifica dispositivos que estão com problemas, estamos mais bem equipados para recolher informações do dispositivo e diagnosticar problemas. **Ao aceitar a Atualização 2, permite-nos fornecer este suporte proactivo**.    

## <a name="issues-fixed-in-update-2"></a>Problemas corrigidos na Atualização 2
As tabelas que se seguem fornecem um resumo das questões que foram corrigidas nas Atualizações 2.    

| N.º | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Interfaces de rede |Após uma atualização para a Atualização 1, o serviço StorSimple Manager informou que as portas Data2 e Data3 falharam num controlador. Esta questão foi corrigida. |Yes |No |
| 2 |Atualizações |Após uma atualização para a Atualização 1, ocorreram alertas de alarme sonoros no portal clássico Azure em vários dispositivos. Esta questão foi corrigida. |Yes |No |
| 3 |Autenticação openstack |Ao utilizar o Openstack como fornecedor de serviços na nuvem, pode receber um erro que a sua cadeia de autenticação em nuvem era demasiado comprida. Este problema foi corrigido. |Yes |No |

## <a name="known-issues-in-update-2"></a>Problemas conhecidos na Atualização 2
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
| 20 |volumes afixados localmente |Se tentar converter um volume hierárquico (criado e clonado com a Atualização 1.2 ou mais cedo) para um volume fixado localmente e o seu dispositivo estiver a ficar sem espaço ou se houver uma falha na nuvem, então o clone(s) pode ser corrompido. |Este problema ocorre apenas com volumes que foram criados e clonados com software pré-Update 2. Este deve ser um cenário pouco frequente. | | |
| 21 |Conversão de volume |Não atualize os ACRs ligados a um volume enquanto estiver em curso uma conversão de volume (tiered to local fixed or vice-versa). A atualização dos ACRs pode resultar em corrupção de dados. |Se necessário, atualize os ACRs antes da conversão do volume e não entede mais atualizações de ACR enquanto a conversão estiver em curso. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Atualizações de controlador e firmware na Atualização 2
Esta versão atualiza o controlador e o firmware do disco no seu dispositivo.

* Para obter mais informações sobre a atualização do firmware LSI, consulte o artigo base do Microsoft Knowledge 3121900. 
* Para obter mais informações sobre a atualização do firmware do disco, consulte o artigo base do Microsoft Knowledge 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Atualizações de dispositivos virtuais no Update 2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais terão de ser criados. 

## <a name="next-step"></a>Passo seguinte
Saiba como instalar o [Update 2](./storsimple-8000-install-update-5.md) no seu dispositivo StorSimple.