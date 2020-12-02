---
title: Planejamento para uma implantação de Ficheiros Azure / Microsoft Docs
description: Compreenda o planeamento de uma implantação de Ficheiros Azure. Pode montar diretamente uma partilha de ficheiros Azure ou partilhar ficheiros cache Azure no local com o Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 650ee1fc9e0e1941a7a3655bca1c75950ab878dd
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492119"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planear uma implementação dos Ficheiros do Azure
[Os Ficheiros Azure](storage-files-introduction.md) podem ser implementados de duas maneiras principais: montando diretamente as ações de ficheiros Azure sem servidor ou caching Azure file shares on-in usando Azure File Sync. Qual a opção de implementação que escolhe altera as coisas que precisa de considerar como planeia para a sua implantação. 

- **Montagem direta de uma partilha de ficheiros Azure**: Uma vez que os Ficheiros Azure fornecem acesso ao Bloco de Mensagens do Servidor (SMB) ou ao Sistema de Ficheiros de Rede (NFS), pode montar ações de ficheiros Azure no local ou na nuvem utilizando os clientes padrão SMB ou NFS disponíveis no seu SO. Como as ações de ficheiros Azure são sem servidor, a implementação para cenários de produção não requer a gestão de um servidor de ficheiros ou de um dispositivo NAS. Isto significa que não tem de aplicar patches de software ou trocar discos físicos. 

- **A partilha de ficheiros Cache Azure no local com o Azure File Sync**: O Azure File Sync permite centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma um Servidor Windows no local (ou nuvem) numa cache rápida da sua partilha de ficheiros Azure SMB. 

Este artigo aborda principalmente considerações de implementação para a implementação de uma partilha de ficheiros Azure a ser montada diretamente por um cliente no local ou na nuvem. Para planear uma implementação de Azure File Sync, consulte [o Planeamento para uma implementação de Sincronização de Ficheiros Azure](storage-sync-files-planning.md).

## <a name="available-protocols"></a>Protocolos disponíveis

A Azure Files oferece dois protocolos que podem ser utilizados na montagem das suas ações de ficheiros, SMB e Sistema de Ficheiros de Rede (NFS). Para mais informações sobre estes protocolos, consulte os [protocolos de partilha de ficheiros Azure](storage-files-compare-protocols.md).

> [!IMPORTANT]
> A maior parte do conteúdo deste artigo aplica-se apenas às ações da SMB. Tudo o que se aplica às ações da NFS indicará especificamente que é aplicável.

## <a name="management-concepts"></a>Conceitos de gestão
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Ao colocar ações de ficheiros Azure em contas de armazenamento, recomendamos:

- Apenas a implementação de ações de ficheiros Azure em contas de armazenamento com outras ações de ficheiros Azure. Embora as contas de armazenamento do GPv2 permitam ter contas de armazenamento mistas, uma vez que os recursos de armazenamento, como as ações de ficheiros Azure e os contentores blob, partilham os limites da conta de armazenamento, misturar recursos em conjunto pode dificultar a resolução de problemas de desempenho mais tarde. 

- Prestar atenção às limitações de IOPS de uma conta de armazenamento ao implementar ações de ficheiros Azure. Idealmente, você mapeia ações de arquivo 1:1 com contas de armazenamento, no entanto isso pode nem sempre ser possível devido a vários limites e restrições, tanto da sua organização como da Azure. Quando não for possível ter apenas uma ação de ficheiro implantada numa única conta de armazenamento, considere quais as ações que serão altamente ativas e quais as ações menos ativas para garantir que as ações de ficheiros mais quentes não sejam colocadas na mesma conta de armazenamento em conjunto.

- Apenas implemente contas de GPv2 e FileStorage e atualize o GPv1 e as contas de armazenamento clássicas quando as encontrar no seu ambiente. 

## <a name="identity"></a>Identidade
Para aceder a uma partilha de ficheiros Azure, o utilizador da partilha de ficheiros deve ser autenticado e ter autorização para aceder à partilha. Isto é feito com base na identidade do utilizador que acede à partilha de ficheiros. A Azure Files integra-se com três principais fornecedores de identidade:
- **No local Serviços de Domínio de Diretório Ativo (DS AD ou DS no local)**: As contas de armazenamento Azure podem ser unidas a um serviço de domínio de propriedade do cliente, de propriedade do cliente, serviços de domínio de diretório ativo, tal como um servidor de ficheiros do Windows Server ou um dispositivo NAS. Pode implantar um controlador de domínio no local, num VM Azure, ou mesmo como VM em outro fornecedor de nuvem; O Azure Files é agnóstico até onde o seu controlador de domínio está hospedado. Uma vez que uma conta de armazenamento é unida ao domínio, o utilizador final pode montar uma partilha de ficheiro com a conta de utilizador com a qual assinou no seu PC. A autenticação baseada em AD utiliza o protocolo de autenticação Kerberos.
- **Azure Ative Directory Domain Services (Azure AD DS)**: A azure AD DS fornece um controlador de domínio gerido pela Microsoft que pode ser usado para recursos Azure. O domínio que une a sua conta de armazenamento ao Azure AD DS proporciona benefícios semelhantes ao domínio que o une a um Ative Directory de propriedade do cliente. Esta opção de implementação é mais útil para cenários de elevação e mudança de aplicações que requerem permissões baseadas em AD. Uma vez que a Azure AD DS fornece autenticação baseada em AD, esta opção também utiliza o protocolo de autenticação Kerberos.
- **Chave da conta de armazenamento Azure**: As ações de ficheiros Azure também podem ser montadas com uma chave de conta de armazenamento Azure. Para montar uma partilha de ficheiros desta forma, o nome da conta de armazenamento é usado como nome de utilizador e a chave da conta de armazenamento é usada como senha. A utilização da chave de conta de armazenamento para montar a partilha de ficheiros Azure é efetivamente uma operação de administrador, uma vez que a partilha de ficheiros montada terá permissões completas em todos os ficheiros e pastas da partilha, mesmo que tenham ACLs. Ao utilizar a chave de conta de armazenamento para montar sobre o SMB, é utilizado o protocolo de autenticação NTLMv2.

Para os clientes que migram de servidores de ficheiros no local ou criam novas ações de ficheiros em Ficheiros Azure destinados a comportarem-se como servidores de ficheiros Windows ou aparelhos NAS, o domínio que une a sua conta de armazenamento ao **Ative Directory do Cliente** é a opção recomendada. Para saber mais sobre o domínio que une a sua conta de armazenamento a um Ative Directory de propriedade do cliente, consulte [a visão geral do Azure Files Ative Directory](storage-files-active-directory-overview.md).

Se pretender utilizar a chave da conta de armazenamento para aceder às suas ações de ficheiros Azure, recomendamos a utilização de pontos finais de serviço, conforme descrito na secção [Networking.](#networking)

## <a name="networking"></a>Rede
As ações de ficheiros Azure estão acessíveis a partir de qualquer lugar através do ponto final público da conta de armazenamento. Isto significa que os pedidos autenticados, tais como pedidos autorizados pela identidade de início de súmã de um utilizador, podem ter origem seguramente dentro ou fora de Azure. Em muitos ambientes de clientes, uma montagem inicial da partilha de ficheiros Azure na sua estação de trabalho no local falhará, mesmo que os suportes dos VMs Azure tenham sucesso. A razão para isso é que muitas organizações e fornecedores de serviços de internet (ISPs) bloqueiam a porta que o SMB usa para comunicar, porta 445. Para ver o resumo de ISPs que permitem ou não o acesso a partir da porta 445, aceda a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para desbloquear o acesso à sua partilha de ficheiros Azure, tem duas opções principais:

- Desbloqueie a porta 445 para a rede de instalações da sua organização. As ações de ficheiros Azure só podem ser acedidas externamente através do ponto final público utilizando protocolos de segurança na Internet, tais como SMB 3.0 e a API FileREST. Esta é a forma mais fácil de aceder à sua partilha de ficheiros Azure a partir das instalações, uma vez que não requer uma configuração avançada de rede para além de alterar as regras de saída da sua organização, no entanto, recomendamos que remova o legado e versões prepretadas do protocolo SMB, nomeadamente SMB 1.0. Para aprender a fazê-lo, consulte [o Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) e [o Linux de Segurança](storage-how-to-use-files-linux.md#securing-linux).

- Aceda a ações de ficheiros Azure sobre uma ligação ExpressRoute ou VPN. Ao aceder à sua partilha de ficheiros Azure através de um túnel de rede, pode montar a sua partilha de ficheiros Azure como uma partilha de ficheiros no local, uma vez que o tráfego SMB não atravessa o seu limite organizacional.   

Embora do ponto de vista técnico seja consideravelmente mais fácil montar as suas ações de ficheiros Azure através do ponto final público, esperamos que a maioria dos clientes opte por montar as suas ações de ficheiroS Azure através de uma ligação ExpressRoute ou VPN. A montagem com estas opções é possível com as ações da SMB e da NFS. Para isso, terá de configurar o seguinte para o seu ambiente:  

- **Fazer túneis de rede utilizando expressRoute, Site-to-Site ou VPN ponto-a-local**: Fazer túneis numa rede virtual permite aceder a partilhas de ficheiros Azure a partir do local, mesmo que a porta 445 esteja bloqueada.
- **Pontos finais privados**: Os pontos finais privados dão à sua conta de armazenamento um endereço IP dedicado a partir do espaço de endereço da rede virtual. Isto permite a escavação de rede sem necessidade de abrir redes no local até todas as gamas de endereços IP detidas pelos clusters de armazenamento Azure. 
- **DNS reencaminhamento**: Configure o seu DNS no local para resolver o nome da sua conta de armazenamento (isto é, `storageaccount.file.core.windows.net` para as regiões de nuvem pública) para resolver o endereço IP dos seus pontos finais privados.

Para planear a rede associada à implementação de uma partilha de ficheiros Azure, consulte [considerações de networking de ficheiros Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Encriptação
O Azure Files suporta dois tipos diferentes de encriptação: encriptação em trânsito, que se relaciona com a encriptação utilizada na montagem/acesso à partilha de ficheiros Azure, e encriptação em repouso, que se relaciona com a forma como os dados são encriptados quando são armazenados no disco. 

### <a name="encryption-in-transit"></a>Encriptação de dados em circulação

> [!IMPORTANT]
> Esta secção cobre a encriptação em detalhes de trânsito para ações SMB. Para obter detalhes sobre encriptação em trânsito com ações NFS, consulte [Segurança](storage-files-compare-protocols.md#security).

Por padrão, todas as contas de armazenamento Azure têm encriptação em trânsito ativada. Isto significa que quando monta uma partilha de ficheiros sobre o SMB ou acede-lo através do protocolo FileREST (por exemplo através do portal Azure, PowerShell/CLI ou Azure SDKs), os Ficheiros Azure só permitirão a ligação se for feita com SMB 3.0+ com encriptação ou HTTPS. Os clientes que não suportam SMB 3.0 ou clientes que suportem encriptação SMB 3.0 mas não SMB não poderão montar a partilha de ficheiros Azure se a encriptação em trânsito estiver ativada. Para obter mais informações sobre quais os sistemas operativos que suportam SMB 3.0 com encriptação, consulte a nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux.](storage-how-to-use-files-linux.md) Todas as versões atuais do suporte de PowerShell, CLI e SDKs HTTPS.  

Pode desativar a encriptação em trânsito para uma conta de armazenamento Azure. Quando a encriptação é desativada, os Ficheiros Azure também permitirão chamadas SMB 2.1, SMB 3.0 sem encriptação e chamadas de API de ficheiros não encriptadas em HTTP. A principal razão para desativar a encriptação em trânsito é suportar uma aplicação antiga que deve ser executada num sistema operativo mais antigo, como o Windows Server 2008 R2 ou a distribuição linux mais antiga. A Azure Files só permite ligações SMB 2.1 na mesma região de Azure que a partilha de ficheiros Azure; um cliente SMB 2.1 fora da região de Azure da partilha de ficheiros Azure, como no local ou numa região de Azure diferente, não poderá aceder à partilha de ficheiros.

Recomendamos vivamente garantir que a encriptação dos dados em trânsito está ativada.

Para obter mais informações sobre encriptação em trânsito, consulte [a necessidade de transferência segura no armazenamento Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Encriptação inativa
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Proteção de dados
A Azure Files tem uma abordagem em várias camadas para garantir que os seus dados são apoiados, recuperáveis e protegidos contra ameaças à segurança.

### <a name="soft-delete"></a>Eliminação recuperável
A eliminação suave para ações de ficheiros (pré-visualização) é uma definição de nível de conta de armazenamento que lhe permite recuperar a sua parte do ficheiro quando esta é acidentalmente eliminada. Quando uma partilha de ficheiros é eliminada, passa para um estado apagado suave em vez de ser permanentemente apagada. Pode configurar a quantidade de tempo que os dados suaves eliminados são recuperáveis antes de serem permanentemente eliminados, e desembolsar a parte a qualquer momento durante este período de retenção. 

Recomendamos que se apale a exclusão suave para a maioria das ações de ficheiros. Se tiver um fluxo de trabalho em que a eliminação de ações é comum e esperada, pode decidir ter um período de retenção muito curto ou não ter uma eliminação suave ativada.

Para obter mais informações sobre a eliminação suave, consulte [Prevenir a eliminação acidental de dados](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
Pode fazer cópias do seu ficheiro Azure através [de imagens de partilha,](./storage-snapshots-files.md)que são cópias pontuais e pontuais da sua parte. Os instantâneos são incrementais, o que significa que só contêm tantos dados como mudou desde o instantâneo anterior. Pode ter até 200 instantâneos por ação de ficheiro e retê-los até 10 anos. Pode tirar manualmente estas fotos no portal Azure, via PowerShell, ou interface de linha de comando (CLI), ou pode utilizar [a Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). As imagens instantâneas são armazenadas dentro da sua parte do ficheiro, o que significa que se eliminar a sua parte do ficheiro, as suas imagens também serão eliminadas. Para proteger as cópias de segurança instantâneas da eliminação acidental, certifique-se de que a eliminação suave está ativada para a sua parte.

[A Azure Backup para ações de ficheiros Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) lida com o agendamento e retenção de instantâneos. As suas capacidades de avô-pai-filho (GFS) significam que você pode tomar fotos diárias, semanais, mensais e anualmente, cada uma com o seu próprio período de retenção distinto. O Azure Backup também orquestra a ativação de eliminação suave e recebe um bloqueio de eliminação numa conta de armazenamento assim que qualquer partilha de ficheiros dentro dela estiver configurada para cópia de segurança. Por último, o Azure Backup fornece determinadas capacidades de monitorização e alerta chave que permitem aos clientes ter uma visão consolidada do seu espólio de backup.

Pode executar restauros de nível de item e de nível de partilha no portal Azure utilizando a Cópia de Segurança Azure. Tudo o que precisa de fazer é escolher o ponto de restauro (uma determinada imagem instantânea), o ficheiro ou diretório em particular, se relevante, e, em seguida, a localização (original ou alternativa) a que deseja restaurar. O serviço de cópias de segurança e mostra o progresso da restauração no portal.

Para obter mais informações sobre a cópia de segurança, consulte [a cópia de segurança do ficheiro Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Proteção Avançada de Ameaças para Ficheiros Azure (pré-visualização)
Advanced Threat Protection (ATP) for Azure Storage fornece uma camada adicional de inteligência de segurança que fornece alertas quando deteta atividade anómala na sua conta de armazenamento, por exemplo, tentativas incomuns de acesso à conta de armazenamento. O ATP também executa a análise de reputação de malware e irá alertar para malware conhecido. Pode configurar ATP num nível de subscrição ou conta de armazenamento através do Azure Security Center. 

Para obter mais informações, consulte [a proteção contra ameaças avançadas para o armazenamento Azure](../common/azure-defender-storage-configure.md).

## <a name="storage-tiers"></a>Camadas de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Em geral, as funcionalidades da Azure Files e a interoperabilidade com outros serviços são as mesmas entre ações de ficheiros premium e ações de ficheiros padrão (incluindo ações de ficheiros otimizadas, quentes e cool), no entanto existem algumas diferenças importantes:
- **Modelo de faturação**
    - As ações de ficheiros premium são faturadas usando um modelo de faturação provisão, o que significa que você paga o preço fixo pelo armazenamento que você presta em vez de quanto armazenamento você usa. Não existem custos adicionais para transações e metadados em repouso.
    - As ações de ficheiros standard são faturadas usando um modelo pay-as-you-go, que inclui um custo base de armazenamento para o armazenamento que você está realmente consumindo e, em seguida, um custo adicional de transação com base na forma como você usa a ação. Com as ações de ficheiro padrão, a sua conta aumentará se utilizar (ler/escrever/montar) o ficheiro Azure partilhar mais.
- **Opções de redundância**
    - As ações de ficheiros premium só estão disponíveis para armazenamento localmente redundante (LRS) e zona redundante (ZRS).
    - As ações de ficheiros standard estão disponíveis para o armazenamento localmente redundante, redundante, geo-redundante (GRS) e geo-zona redundante (GZRS).
- **Tamanho máximo da partilha de ficheiros**
    - As ações de ficheiros premium podem ser a provisionadas até 100 TiB sem qualquer trabalho adicional.
    - Por predefinição, as ações de ficheiros padrão podem abranger apenas até 5 TiB, embora o limite de ações possa ser aumentado para 100 TiB optando pela bandeira da conta de armazenamento *de grandes ficheiros.* As ações de ficheiros standard só podem abranger até 100 TiB para contas de armazenamento redundantes locais ou de zonas redundantes. Para obter mais informações sobre o aumento do tamanho das ações de ficheiros, consulte [Ativar e criar grandes ações de ficheiros](./storage-files-how-to-create-large-file-share.md).
- **Disponibilidade regional**
    - As ações de ficheiros premium estão disponíveis na maioria das regiões de Azure, com exceção de algumas regiões. O apoio redundante da zona está disponível num subconjunto de regiões. Para saber se as ações de ficheiros premium estão atualmente disponíveis na sua região, consulte os [produtos disponíveis por página da região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para a Azure. Para saber quais as regiões que suportam o ZRS, consulte [o armazenamento redundante da zona.](../common/storage-redundancy.md#zone-redundant-storage) Para nos ajudar a priorizar novas regiões e funcionalidades de nível premium, preencha este [inquérito.](https://aka.ms/pfsfeedback)
    - As ações de ficheiros standard estão disponíveis em todas as regiões do Azure.
- O Azure Kubernetes Service (AKS) suporta ações de ficheiros premium na versão 1.13 e posterior.

Uma vez que uma partilha de ficheiros é criada como um prémio ou uma partilha de ficheiro padrão, não é possível convertê-la automaticamente para o outro nível. Se quiser mudar para o outro nível, tem de criar uma nova partilha de ficheiros nesse nível e copiar manualmente os dados da sua parte original para a nova partilha que criou. Recomendamos a utilização `robocopy` para Windows ou para `rsync` macOS e Linux para executar esta cópia.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Compreensão do provisionamento das ações de ficheiros premium
As ações de ficheiros premium são abastecadas com base num rácio GiB/IOPS/produção fixo. Todos os tamanhos de ações são oferecidos de base/produção mínima e autorizados a rebentar. Para cada GiB a provisionado, a ação será emitida como IOPS/produção mínima e um IOPS e 0,1 MiB/s até aos limites máximos por ação. O fornecimento mínimo permitido é de 100 GiB com o mínimo IOPS/produção. 

Todas as ações premium são oferecidas rebentando gratuitamente com a melhor base de esforço. Todos os tamanhos de ações podem explodir até 4.000 IOPS ou até três IOPS por GiB provisitado, o que fornecer um IOPS de maior explosão para a parte. Todas as ações suportam rebentar por uma duração máxima de 60 minutos num limite máximo de rajada. As novas ações começam com o crédito total com base na capacidade provisida.

As ações devem ser abastradas em 1 incremento GiB. O tamanho mínimo é de 100 GiB, o próximo tamanho é 101 GiB, e assim por diante.

> [!TIP]
> IOPS de base = 400 + 1 * GiB provisionado. (Até um máximo de 100.000 IOPS).
>
> Limite de explosão = MAX (4.000, 3 * PIOS de base). (qualquer que seja o limite maior, até um máximo de 100.000 IOPS).
>
> taxa de egress = 60 MiB/s + 0,06 * GiB a provisionado
>
> taxa de entrada = 40 MiB/s + 0,04 * GiB a provisionado

O tamanho das ações previstas é especificado por quota de ações. A quota de ações pode ser aumentada a qualquer momento, mas só pode ser diminuída após 24 horas desde o último aumento. Depois de esperar 24 horas sem um aumento de quota, pode diminuir a quota de ações quantas vezes quiser, até que a aumente novamente. As alterações na escala IOPS/Produção serão eficazes dentro de poucos minutos após a mudança de tamanho.

É possível diminuir o tamanho da sua parte abaixo do seu GiB usado. Se o fizer, não perderá dados mas, ainda será cobrado pelo tamanho utilizado e receberá o desempenho (IOPS de base, produção e IOPS rebentado) da parte atada, e não do tamanho utilizado.

A tabela a seguir ilustra alguns exemplos destas fórmulas para as dimensões das ações previstas:

|Capacidade (GiB) | IOPS de linha de base | IOPS de explosão | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Até 4.000     | 66   | 44   |
|500         | 900     | Até 4.000  | 90   | 60   |
|1,024       | 1,424   | Até 4.000   | 122   | 81   |
|5,120       | 5,520   | Até 15.360  | 368   | 245   |
|10,240      | 10,640  | Até 30.720  | 675   | 450   |
|33,792      | 34,192  | Até 100.000 | 2,088 | 1,392   |
|51,200      | 51,600  | Até 100.000 | 3,132 | 2,088   |
|102,400     | 100.000 | Até 100.000 | 6,204 | 4,136   |

É importante notar que o desempenho efetivo das partilhas de ficheiros está sujeito a limites de rede de máquinas, largura de banda de rede disponível, tamanhos de IO, paralelismo, entre muitos outros fatores. Por exemplo, com base em testes internos com tamanhos IO de leitura/escrita de 8 KiB, uma única máquina virtual Windows sem O Multicanal SMB ativada, *Standard F16s_v2,* ligada à partilha de ficheiros premium sobre SMB poderia alcançar 20K ler IOPS e 15K escrever IOPS. Com 512 tamanhos de IO de leitura/escrita MiB, o mesmo VM poderia alcançar 1.1 GiB/s e 370 MiB/s ingress produção. O mesmo cliente pode alcançar até \~ 3x de desempenho se o SMB Multichannel estiver ativado nas ações premium. Para atingir a escala máxima de desempenho, [ative o SMB Multicanal](storage-files-enable-smb-multichannel.md) e espalhe a carga por vários VMs. Consulte o [guia de desempenho multicanal](storage-files-smb-multichannel-performance.md) e [resolução de problemas](storage-troubleshooting-files-performance.md) SMB para alguns problemas de desempenho comuns e soluções alternativas.

#### <a name="bursting"></a>Estourando
Se a sua carga de trabalho necessitar do desempenho extra para satisfazer a procura máxima, a sua parte pode usar créditos de rutura para ir acima do limite de IOPS de linha de base para oferecer o desempenho de partilha que precisa para satisfazer a procura. As ações de ficheiros premium podem rebentar o seu IOPS até 4.000 ou até um fator de três, o que for um valor mais elevado. A explosão é automatizada e funciona com base num sistema de crédito. A explosão funciona com uma melhor base de esforço e o limite de explosão não é uma garantia, as ações de ficheiro podem rebentar *até ao* limite por uma duração máxima de 60 minutos.

Os créditos acumulam-se num balde de rutura sempre que o tráfego para a sua parte de ficheiro está abaixo do IOPS de base. Por exemplo, uma quota de 100 GiB tem 500 IOPS de base. Se o tráfego real na parte foi de 100 IOPS para um intervalo específico de 1 segundo, então os 400 IOPS não reutilizados são creditados a um balde de explosão. Da mesma forma, uma quota de TiB ociosa 1, acumula crédito rebentado em 1.424 IOPS. Estes créditos serão então utilizados mais tarde quando as operações excederem o IOPS de base.

Sempre que uma ação exceder o IOPS de base e tiver créditos num balde de rutura, irá rebentar na taxa máxima de pico permitida. As ações podem continuar a rebentar enquanto os créditos permanecerem, até ao máximo de 60 minutos de duração, mas isso baseia-se no número de créditos acumulados. Cada IO para além do IOPS de base consome um crédito e uma vez consumidos todos os créditos, a parte regressaria ao IOPS de base.

Os créditos de ações têm três estados:

- Acumulando, quando a parte do ficheiro está a usar menos do que o IOPS de base.
- Em declínio, quando a partilha de ficheiros está a utilizar mais do que o IOPS de base e no modo de rebentamento.
- Constante, a partilha de ficheiros está a usar exatamente o IOPS de base, ou não existem créditos acumulados ou utilizados.

As novas ações de ficheiros começam com o número total de créditos no seu balde de rebentamento. Os créditos de rutura não serão acumulados se o IOPS de ações cair abaixo do IOPS de base devido ao estrangulamento pelo servidor.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Permitir que as ações de ficheiros padrão se estendem até 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Limitações
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migração
Em muitos casos, não irá estabelecer uma nova partilha de ficheiros net para a sua organização, mas sim migrar uma parte de ficheiro existente de um servidor de ficheiros no local ou dispositivo NAS para Azure Files. Escolher a estratégia e ferramenta de migração certas para o seu cenário é importante para o sucesso da sua migração. 

O [artigo de visão geral da migração](storage-files-migration-overview.md) cobre brevemente o básico e contém uma tabela que o leva a guias de migração que provavelmente cobrem o seu cenário.

## <a name="next-steps"></a>Passos seguintes
* [Planeamento para uma implementação de sincronização de ficheiros Azure](storage-sync-files-planning.md)
* [Implantação de ficheiros Azure](storage-files-deployment-guide.md)
* [Implementação de sincronização de ficheiros Azure](storage-sync-files-deployment-guide.md)
* [Confira o artigo visão geral da migração para encontrar o guia de migração para o seu cenário](storage-files-migration-overview.md)
