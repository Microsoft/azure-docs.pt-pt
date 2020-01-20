---
title: Notas de versão da atualização 1,0 do StorSimple virtual array
description: Descreve problemas e resoluções críticos em aberto para a matriz virtual StorSimple que executa a atualização 1,0.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 48dec3a87ab540af224ae4ac59dd37cee7c9d0ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271346"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Notas de versão da atualização 1,0 do StorSimple virtual array

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para Microsoft Azure StorSimple atualizações de matriz virtual.

As notas de versão são atualizadas continuamente, e como problemas críticos que exigem uma solução alternativa são descobertos, eles são adicionados. Antes de implantar sua matriz virtual StorSimple, examine atentamente as informações contidas nas notas de versão.

A atualização 1,0 corresponde à versão de software **10.0.10296.0**.

> [!IMPORTANT]
> - As atualizações causam interrupção e reiniciam o dispositivo. Se a e/s estiver em andamento, o dispositivo incorrerá em tempo de inatividade. Para obter instruções detalhadas sobre como aplicar a atualização, acesse [instalar a atualização 1,0](storsimple-virtual-array-install-update-1.md).
>
> - A atualização 1 só estará disponível por meio do portal do Azure se o dispositivo estiver executando a atualização 0,6.

## <a name="whats-new-in-update-10"></a>O que há de novo na atualização 1,0

**A atualização 1,0 contém alterações relacionadas à autenticação do serviço StorSimple Device Manager e deve ser implantada o mais cedo.** Essa atualização contém os seguintes aprimoramentos e correções de bugs:

 - O **uso de Azure Active Directory (AAD) para autenticar com o serviço storsimple Device Manager** – da atualização 1,0 em diante, Azure Active Directory é usado para autenticar com o serviço de Device Manager do storsimple. O antigo mecanismo de autenticação será preterido em dezembro de 2017. Todos os usuários devem incluir as novas URLs de autenticação em suas regras de firewall. Para obter mais informações, acesse URLs de autenticação listadas nos [requisitos de rede para sua matriz virtual StorSimple](storsimple-ova-system-requirements.md).
 
    Se a URL de autenticação não estiver incluída nas regras de firewall, os usuários verão um alerta crítico de que seu dispositivo StorSimple não pôde se autenticar no serviço. Se os usuários veem esse alerta, eles precisam incluir a nova URL de autenticação. Para obter mais informações, acesse [alertas de rede do StorSimple](storsimple-virtual-array-manage-alerts.md).

 - **Melhoria de desempenho** -foram feitas várias correções de bugs para melhorar as velocidades de leituras de nuvem, camadas e limites de camadas. Como resultado, o desempenho de backup e restauração foi aprimorado para dispositivos iSCSI e de servidor de arquivos.

 - **Melhoria da coleta de lixo** – esta versão tem correções de bugs que melhoram o desempenho do ciclo de coleta de lixo quando a conta de dispositivo e de armazenamento está em duas regiões distantes.

 - **Aperfeiçoamento de log** – esta versão contém melhorias no registro em log relacionados à coleta de lixo e ao caminho de e/s.


## <a name="issues-fixed-in-update-10"></a>Problemas corrigidos na atualização 1,0

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Autenticação baseada no AAD| Esta versão contém alterações que permitem que o AAD autentique com o Device Manager StorSimple.|
| 2 |Coleta de lixo| Esse problema foi relatado em um site do cliente em que o dispositivo e as contas de armazenamento estão em regiões diferentes e o cliente relatou erros intermitentes de rede, afetando a cobrança. Nesta versão, esse problema foi corrigido. |
| 3 |Desempenho| Esta versão contém alterações que resultam na melhoria do desempenho de leitura/leituras de nuvem/entrada na camada.|
| 4 |Atualizar| Houve um problema com a atualização na versão anterior que resultou em falhas de backup no site do cliente. Esse problema foi corrigido nesta versão.|

## <a name="known-issues-in-update-10"></a>Problemas conhecidos na atualização 1,0

A tabela a seguir fornece um resumo dos problemas conhecidos para a matriz virtual StorSimple e inclui a versão de problemas anotada nas versões anteriores.

| Não. | Funcionalidade | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |As matrizes virtuais criadas na versão de visualização não podem ser atualizadas para uma versão de disponibilidade geral com suporte. |Essas matrizes virtuais devem passar por failover para a versão de disponibilidade geral usando um fluxo de trabalho de DR (recuperação de desastre). |
| **2.** |Disco de dados provisionado |Depois de ter provisionado um disco de dados de um determinado tamanho especificado e criado a matriz virtual StorSimple correspondente, você não deve expandir ou reduzir o disco de dados. Tentar fazer resulta em uma perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo está ingressado no domínio, a aplicação de uma diretiva de grupo pode afetar negativamente a operação do dispositivo. |Verifique se sua matriz virtual está em sua própria UO (unidade organizacional) para Active Directory e se nenhum GPO (objeto de diretiva de grupo) está aplicado a ela. |
| **4.** |Interface do usuário da Web local |Se os recursos de segurança aprimorados estiverem habilitados no Internet Explorer (IE ESC), algumas páginas da interface do usuário da Web local, como solução de problemas ou manutenção, poderão não funcionar corretamente. Os botões nessas páginas também podem não funcionar. |Desative os recursos de segurança aprimorados no Internet Explorer. |
| **5.** |Interface do usuário da Web local |Em uma máquina virtual do Hyper-V, as interfaces de rede na interface do usuário da Web são exibidas como interfaces de 10 Gbps. |Esse comportamento é uma reflexão do Hyper-V. O Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** |Volumes ou compartilhamentos em camadas |Não há suporte para o bloqueio de intervalo de bytes para aplicativos que funcionam com os volumes em camadas do StorSimple. Se o bloqueio de intervalo de bytes estiver habilitado, a camada do StorSimple não funcionará. |As medidas recomendadas incluem: <br></br>Desative o bloqueio de intervalo de bytes na lógica do aplicativo.<br></br>Escolha colocar dados para este aplicativo em volumes fixados localmente em vez de volumes em camadas.<br></br>*Advertência*: ao usar volumes fixados localmente e o bloqueio de intervalo de bytes estiver habilitado, o volume fixado localmente poderá ficar online mesmo antes da conclusão da restauração. Nesses casos, se uma restauração estiver em andamento, você deverá aguardar a conclusão da restauração. |
| **7.** |Compartilhamentos em camadas |Trabalhar com arquivos grandes pode resultar em redução da camada. |Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja menor que 3% do tamanho do compartilhamento. |
| **8.** |Capacidade usada para compartilhamentos |Você poderá ver o consumo de compartilhamento quando não houver dados no compartilhamento. Esse consumo ocorre porque a capacidade usada para compartilhamentos inclui metadados. | |
| **9.** |Recuperação após desastre |Você só pode executar a recuperação de desastre de um servidor de arquivos para o mesmo domínio que o dispositivo de origem. A recuperação de desastre para um dispositivo de destino em outro domínio não tem suporte nesta versão. |Isso é implementado em uma versão posterior. Para obter mais informações, acesse [failover e recuperação de desastre para a matriz virtual StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |As matrizes virtuais StorSimple não podem ser gerenciadas por meio do Azure PowerShell nesta versão. |Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio da portal do Azure e da interface do usuário da Web local. |
| **11.** |Alteração da palavra-passe |O console do dispositivo de matriz virtual só aceita entrada no formato de teclado en-US. | |
| **12.** |CHAP |As credenciais CHAP depois de criadas não podem ser removidas. Além disso, se você modificar as credenciais CHAP, precisará colocar os volumes offline e, em seguida, colocá-los online para que a alteração entre em vigor. |Esse problema é abordado em uma versão posterior. |
| **13.** |servidor iSCSI |O ' armazenamento usado ' exibido para um volume iSCSI pode ser diferente no serviço StorSimple Device Manager e no host iSCSI. |O host iSCSI tem a exibição do sistema de arquivos.<br></br>O dispositivo vê os blocos alocados quando o volume estava no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um arquivo em uma pasta tiver um fluxo de dados alternativo (ADS) associado a ele, os anúncios não serão armazenados em backup nem restaurados por meio de recuperação de desastre, clone e recuperação em nível de item. | |
| **15.** |Servidor de ficheiros |Não há suporte para links simbólicos. | |
| **16.** |Servidor de ficheiros |Arquivos protegidos pelo Windows Encrypting File System (EFS) quando copiados ou armazenados no servidor de arquivos do StorSimple virtual array resultam em uma configuração sem suporte.  | |
| **17.** |Atualizações |Se você vir o código de erro: 2359302 (Hex 0x240006) ao tentar instalar um hotfix por meio da interface do usuário local, isso significa que o hotfix já está instalado em seu dispositivo.   | |
| **18.** |Atualizações |Se você usar a interface do usuário da Web local para instalar a atualização 1 em sua matriz virtual, deverá garantir que está executando a atualização 0,6. Se você estiver executando uma versão inferior à atualização 0,6, deverá instalar a atualização 0,6 primeiro e, em seguida, aplicar a atualização 1. Se você instalar diretamente a atualização 1,0 de uma versão anterior à atualização 0,6, perderá algumas atualizações e os gráficos de monitoramento não funcionarão.   | |


## <a name="next-steps"></a>Passos seguintes
[Instale a atualização 1,0](storsimple-virtual-array-install-update-1.md) em sua matriz virtual StorSimple.

## <a name="references"></a>Referências
Procurando uma nota de versão mais antiga? Vá para:
*  [Notas de versão da atualização 0,6 do StorSimple virtual array](storsimple-virtual-array-update-06-release-notes.md)
* [Notas de versão da atualização 0,5 do StorSimple virtual array](storsimple-virtual-array-update-05-release-notes.md)
* [Notas de versão da atualização 0,4 do StorSimple virtual array](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de versão da atualização 0,3 do StorSimple virtual array](storsimple-ova-update-03-release-notes.md)
* [Notas de versão da atualização 0,1 e 0,2 do StorSimple virtual array](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade geral do StorSimple virtual array](storsimple-ova-pp-release-notes.md)
