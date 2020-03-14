---
title: Planeamento para uma implantação de Ficheiros Azure  Microsoft Docs
description: Saiba o que considerar ao planear uma implementação de Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b5bf5cc5c44226236f39a6e32c33ebe346e36eeb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269019"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planear uma implementação dos Ficheiros do Azure
[Os Ficheiros Azure](storage-files-introduction.md) podem ser implementados de duas formas principais: montando diretamente as ações de ficheiros Azure sem servidor ou por cache ingequedem ações de ficheiros Azure no local utilizando o Azure File Sync. Qual a opção de implementação que escolher altera as coisas que precisa de considerar como planeia para a sua implantação. 

- **Montagem direta de uma partilha de ficheiros Azure**: Uma vez que o Azure Files fornece acesso SMB, pode montar ações de ficheiroS Azure no local ou na nuvem utilizando o cliente Padrão SMB disponível no Windows, macOS e Linux. Uma vez que as partilhas de ficheiros Azure são inservidoras, a implementação para cenários de produção não requer a gestão de um servidor de ficheiros ou de um dispositivo NAS. Isto significa que não tem de aplicar patches de software ou trocar discos físicos. 

- **A partilha de ficheiros Cache Azure no local com**o Azure File Sync : Azure File Sync permite centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo ao mesmo tempo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma um Windows Server no local (ou cloud) numa cache rápida da sua partilha de ficheiros Azure. 

Este artigo aborda principalmente considerações de implantação para a implementação de uma parte de ficheiro Azure para ser montada diretamente por um cliente no local ou na nuvem. Para planear uma implementação de Sincronização de Ficheiros Azure, consulte o Planeamento para uma implementação de Sincronização de [Ficheiros Azure](storage-sync-files-planning.md).

## <a name="management-concepts"></a>Conceitos de gestão
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Ao implantar as ações de ficheiros Azure em contas de armazenamento, recomendamos:

- Apenas a implantação de ações de ficheiros Azure em contas de armazenamento com outras ações de ficheiros da Azure. Embora as contas de armazenamento GPv2 lhe permitam ter contas mistas de armazenamento, uma vez que recursos de armazenamento como ações de ficheiros Azure e contentores blob partilham os limites da conta de armazenamento, misturar recursos em conjunto pode dificultar a resolução de problemas problemas de desempenho mais tarde. 

- Prestando atenção às limitações iops de uma conta de armazenamento ao implementar ações de ficheiros Azure. Idealmente, você mapearia as partilhas de ficheiros 1:1 com contas de armazenamento, no entanto isso pode nem sempre ser possível devido a vários limites e restrições, tanto da sua organização como do Azure. Quando não for possível ter apenas uma parte de ficheiro implantada numa conta de armazenamento, considere quais as ações que serão altamente ativas e quais as ações que serão menos ativas para garantir que as ações de ficheiro mais quentes não sejam colocadas na mesma conta de armazenamento em conjunto.

- Apenas implemente contas GPv2 e FileStorage e atualize as contas GPv1 e armazenamento clássica quando as encontrar no seu ambiente. 

## <a name="identity"></a>Identidade
Para aceder a uma partilha de ficheiros Azure, o utilizador da parte do ficheiro deve ser autenticado e ter autorização para aceder à partilha. Isto é feito com base na identidade do utilizador que acede à partilha de ficheiros. A Azure Files integra-se com três principais fornecedores de identidade:
- **Ative Directory** (pré-visualização): As contas de armazenamento do Azure podem ser unidas ao domínio de um Diretório Ativo do Windows Server, propriedade do Windows Server, tal como um servidor de ficheiros do Windows Server ou um dispositivo NAS. O seu Controlador de Domínio de Diretório Ativo pode ser implantado no local, num VM Azure, ou mesmo como VM noutro fornecedor de nuvem; O Azure Files é agnóstico onde o seu DC está hospedado. Uma vez que uma conta de armazenamento é unida, o utilizador final pode montar uma partilha de ficheiros com a conta de utilizador com a que assinou no seu PC. A autenticação baseada em AD utiliza o protocolo de autenticação Kerberos.
- **Azure Ative Directory Domain Services (Azure AD DS)** : O Azure AD DS fornece um controlador de domínio de diretório ativo gerido pela Microsoft que pode ser utilizado para recursos Azure. O domínio que une a sua conta de armazenamento ao Azure AD DS proporciona benefícios semelhantes ao domínio que a une a um Diretório Ativo propriedade do cliente. Esta opção de implementação é mais útil para cenários de elevação e mudança de aplicações que requerem permissões baseadas em AD. Uma vez que o Azure AD DS fornece autenticação baseada em AD, esta opção também utiliza o protocolo de autenticação Kerberos.
- Chave da conta de **armazenamento Azure:** As ações de ficheiros Azure também podem ser montadas com uma chave de conta de armazenamento Azure. Para montar uma partilha de ficheirodesta forma, o nome da conta de armazenamento é usado como nome de utilizador e a chave da conta de armazenamento é usada como senha. A utilização da chave da conta de armazenamento para montar a parte do ficheiro Azure é efetivamente uma operação de administrador, uma vez que a parte de ficheiro montada terá permissões completas para todos os ficheiros e pastas da parte, mesmo que tenham ACLs. Ao utilizar a chave da conta de armazenamento para montar sobre SMB, é utilizado o protocolo de autenticação NTLMv2.

Para os clientes que migram de servidores de ficheiros no local ou para criar novas ações de ficheiros em Ficheiros Azure destinados a comportar-se como servidores de ficheiros Windows ou aparelhos NAS, a junção do domínio à sua conta de armazenamento para o **Ative Directory, propriedade do Cliente,** é a opção recomendada. Para saber mais sobre a adesão ao domínio da sua conta de armazenamento a um Diretório Ativo propriedade do cliente, consulte a [visão geral do Diretório Ativo dos Ficheiros Azure.](storage-files-active-directory-overview.md)

Se pretender utilizar a chave da conta de armazenamento para aceder às suas ações de ficheiroS Azure, recomendamos a utilização de pontos finais de serviço, conforme descrito na secção [Networking.](#networking)

## <a name="networking"></a>Redes
As ações de ficheiros Azure são acessíveis a partir de qualquer lugar através do ponto final da conta de armazenamento. Isto significa que os pedidos autenticados, tais como pedidos autorizados pela identidade de logon de um utilizador, podem originar-se de forma segura dentro ou fora do Azure. Em muitos ambientes de clientes, uma primeira parte do ficheiro Azure na sua estação de trabalho no local falhará, mesmo que os montes de VMs Azure tenham sucesso. A razão para isso é que muitas organizações e fornecedores de serviços de internet (ISPs) bloqueiam a porta que a SMB usa para comunicar, porta 445. Para ver o resumo de ISPs que permitem ou não o acesso a partir da porta 445, aceda a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para desbloquear o acesso à sua partilha de ficheiros Azure, tem duas opções principais:

- Desbloqueie a porta 445 para a rede de instalações da sua organização. As ações de ficheiros Azure só podem ser acedidas externamente através do ponto final do público utilizando protocolos de segurança na Internet, tais como o SMB 3.0 e a API FileREST. Esta é a forma mais fácil de aceder à sua partilha de ficheiros Azure a partir do local, uma vez que não requer uma configuração avançada de networking para além de alterar as regras de saída da sua organização, no entanto, recomendamos que remova o legado e versões depreciadas do SMB protocolo, nomeadamente SMB 1.0. Para aprender a fazê-lo, consulte [a fixação do Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) e [a fixação do Linux](storage-how-to-use-files-linux.md#securing-linux).

- Aceda às ações de ficheiros Azure através de uma ligação ExpressRoute ou VPN. Ao aceder à sua partilha de ficheiros Azure através de um túnel de rede, é possível montar a sua partilha de ficheiros Azure como uma partilha de ficheiros no local, uma vez que o tráfego de SMB não atravessa o limite organizacional.   

Embora do ponto de vista técnico seja consideravelmente mais fácil montar as suas ações de ficheiroS Azure através do ponto final público, esperamos que a maioria dos clientes opte por montar as suas ações de ficheiroS Azure através de uma ligação ExpressRoute ou VPN. Para isso, terá de configurar o seguinte para o seu ambiente:  

- **Túneis de rede utilizando expressRoute, Local-a-Local ou VPN Ponto-a-Local**: A escavação numa rede virtual permite aceder a partilhas de ficheiros Azure a partir do local, mesmo que o porto 445 esteja bloqueado.
- **Pontos finais privados**: Os pontos finais privados dão à sua conta de armazenamento um endereço IP dedicado a partir do espaço de endereço da rede virtual. Isto permite a escavação da rede sem necessidade de abrir redes no local até todas as gamas de endereços IP pertencentes aos clusters de armazenamento Azure. 
- **DNS reencaminhado**: Configure o seu DNS no local para resolver o nome da sua conta de armazenamento (ou seja, `storageaccount.file.core.windows.net` para as regiões públicas de nuvem) para resolver o endereço IP dos seus pontos finais privados.

Para planear a rede associada à implementação de uma partilha de ficheiros Azure, consulte as considerações de [networking do Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Encriptação
O Azure Files suporta dois tipos diferentes de encriptação: encriptação em trânsito, que se relaciona com a encriptação utilizada na montagem/acesso à partilha de ficheiros Azure, e encriptação em repouso, que se relaciona com a forma como os dados são encriptados quando são armazenados no disco. 

### <a name="encryption-in-transit"></a>Encriptação em trânsito
Por padrão, todas as contas de armazenamento do Azure têm encriptação ativada em trânsito. Isto significa que quando montar uma partilha de ficheiros sobre SMB ou acedê-la através do protocolo FileREST (como por exemplo através do portal Azure, PowerShell/CLI ou Azure SDKs), o Azure Files só permitirá a ligação se for feita com SMB 3.0+ com encriptação ou HTTPS. Os clientes que não suportam SMB 3.0 ou clientes que suportem sMB 3.0 mas não encriptação SMB não serão capazes de montar a partilha de ficheiros Azure se a encriptação em trânsito estiver ativada. Para obter mais informações sobre quais os sistemas operativos suportam SMB 3.0 com encriptação, consulte a nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md). Todas as versões atuais do PowerShell, CLI e SDKs suportam HTTPS.  

Pode desativar a encriptação em trânsito para uma conta de armazenamento Azure. Quando a encriptação é desativada, o Azure Files também permitirá chamadas SMB 2.1, SMB 3.0 sem encriptação e chamadas API de FileREST não encriptadas sobre HTTP. A principal razão para desativar a encriptação em trânsito é para suportar uma aplicação antiga que deve ser executada num sistema operativo mais antigo, como o Windows Server 2008 R2 ou a distribuição linux mais antiga. O Azure Files apenas permite ligações SMB 2.1 dentro da mesma região azure que a quota de ficheiros Azure; um cliente SMB 2.1 fora da região Azure da quota de ficheiros Azure, como no local ou numa região do Azure diferente, não poderá aceder à partilha de ficheiros.

Recomendamos vivamente que a encriptação de dados em trânsito esteja ativada.

Para obter mais informações sobre encriptação em trânsito, consulte a necessidade de [transferência segura no armazenamento do Azure.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="encryption-at-rest"></a>Encriptação inativa
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Níveis de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Em geral, as funcionalidades do Azure Files e a interoperabilidade com outros serviços são as mesmas entre as ações de ficheiros premium e as ações de ficheiros padrão, no entanto existem algumas diferenças importantes:
- **Modelo de faturação**
    - As ações de ficheiropremium são faturadas usando um modelo de faturação provisionado, o que significa que você paga por quanto armazenamento você fornecer em vez de quanto armazenamento você realmente pede. 
    - As ações de ficheiropadrão são faturadas usando um modelo pay-as-you-go, que inclui um custo base de armazenamento para quanto armazenamento você está realmente consumindo e, em seguida, um custo adicional de transação com base na forma como você usa a ação. Com as partilhas de ficheiros padrão, a sua conta aumentará se utilizar (ler/escrever/montar) o ficheiro Azure partilha mais.
- **Opções de despedimento**
    - As ações de ficheiropremium só estão disponíveis para armazenamento redundante localmente redundante (LRS) e zona redundante (ZRS). 
    - As ações padrão de ficheiros estão disponíveis para armazenamento localmente redundante, redundante, geo-redundante (GRS) e geozona redundante (GZRS).
- **Tamanho máximo da partilha de ficheiros**
    - As ações de ficheiropremium podem ser provisionadas até 100 TiB sem qualquer trabalho adicional.
    - Por predefinição, as ações de ficheiropadrão podem abranger apenas até 5 TiB, embora o limite de ações possa ser aumentado para 100 TiB, optando pela bandeira de recurso de armazenamento de grandes partes de *ficheiros.* As ações de ficheiros standard só podem abranger até 100 TiB para contas de armazenamento redundantes ou redundantes locais. Para mais informações sobre o aumento  
- **Disponibilidade regional**
    - As ações de ficheiros premium não estão disponíveis em todas as regiões, e o apoio redundante da zona está disponível num subconjunto menor de regiões. Para saber se as ações de ficheiropremium estão atualmente disponíveis na sua região, consulte os [produtos disponíveis por página da região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para o Azure. Para saber quais as regiões que suportam o ZRS, consulte o [suporte da Zona de Disponibilidade Azure por região.](../../availability-zones/az-overview.md#services-support-by-region) Para nos ajudar a priorizar novas regiões e características de nível premium, preencha este [inquérito.](https://aka.ms/pfsfeedback)
    - As ações padrão de ficheiros estão disponíveis em todas as regiões do Azure.
- O Azure Kubernetes Service (AKS) suporta ações de ficheiropremium na versão 1.13 e posteriormente.

Uma vez criada uma parte de ficheiro como um prémio ou uma partilha de ficheiros padrão, não pode convertê-la automaticamente para o outro nível. Se quiser mudar para o outro nível, tem de criar uma nova quota de ficheiro nesse nível e copiar manualmente os dados da sua parte original para a nova partilha que criou. Recomendamos que utilize `robocopy` para Windows ou `rsync` para o macOS e o Linux executarem essa cópia.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Compreensão do provisionamento das ações de ficheiros premium
As ações de ficheiropremium são provisionadas com base numa relação gib/iops/perput fixa. Para cada GiB previsto, a ação será emitida uma entrada IOPS e 0,1 MiB/s até aos limites máximos por ação. O fornecimento mínimo permitido é 100 GiB com min IOPS/entrada.

Numa melhor base de esforço, todas as ações podem rebentar até três IOPS por GiB de armazenamento provisionado por 60 minutos ou mais, dependendo da dimensão da parte. As novas ações começam com o crédito total rebentado com base na capacidade provisionada.

As ações devem ser aprovisionadas em incrementos de 1 GiB. O tamanho mínimo é 100 GiB, o próximo tamanho é 101 GiB e assim por diante.

> [!TIP]
> IOPS base = 1 * giB provisionado. (Até um máximo de 100.000 IOPS).
>
> Limite de rutura = 3 * IOPS de base. (Até um máximo de 100.000 IOPS).
>
> taxa de egress = 60 MiB/s + 0,06 * giB provisionado
>
> taxa de entrada = 40 MiB/s + 0,04 * giB provisionado

O tamanho das ações provisionadas é especificado por quota de ações. A quota de ações pode ser aumentada a qualquer momento, mas só pode ser diminuída após 24 horas desde o último aumento. Depois de esperar 24 horas sem um aumento de quota, pode diminuir a quota de partilha quantas vezes quiser, até que volte a aumentá-la. As alterações na escala IOPS/Deputadas serão eficazes dentro de alguns minutos após a alteração do tamanho.

É possível diminuir o tamanho da sua quota provisionada abaixo do Seu GiB usado. Se o fizer, não perderá dados, mas continuará a ser faturado pelo tamanho utilizado e receberá o desempenho (IOPS de base, entrada e iOPS rebentado) da parte provisionada, e não pelo tamanho utilizado.

O quadro que se segue ilustra alguns exemplos destas fórmulas para as dimensões das ações previstas:

|Capacidade (GiB) | IOPS de base | IOPS rebentando | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Até 300     | 66   | 44   |
|500         | 500     | Até 1.500   | 90   | 60   |
|1,024       | 1,024   | Até 3.072   | 122   | 81   |
|5,120       | 5,120   | Até 15.360  | 368   | 245   |
|10,240      | 10,240  | Até 30.720  | 675 | 450   |
|33,792      | 33,792  | Até 100.000 | 2,088 | 1,392   |
|51,200      | 51,200  | Até 100.000 | 3,132 | 2,088   |
|102,400     | 100,000 | Até 100.000 | 6,204 | 4,136   |

> [!NOTE]
> O desempenho das partilhas de ficheiros está sujeito a limites de rede de máquinas, largura de banda de rede disponível, tamanhos IO, paralelismo, entre muitos outros fatores. Por exemplo, com base em testes internos com 8 tamanhos de IO de leitura/escrita KiB, uma única máquina virtual do Windows, *Standard F16s_v2*, ligada à partilha de ficheiros premium sobre SMB poderia atingir IOPS de leitura de 20K e 15K escrever IOPS. Com 512 tamanhos de IO de leitura/escrita MiB, o mesmo VM poderia alcançar 1.1 GiB/s egress e 370 MiB/s entrada de entrada. Para atingir a escala máxima de desempenho, espalhe a carga por vários VMs. Consulte o guia de resolução de [problemas](storage-troubleshooting-files-performance.md) para alguns problemas comuns de desempenho e sobras.

#### <a name="bursting"></a>Rebentando
As ações de ficheiropremium podem rebentar o seu IOPS até um fator de três. A explosão é automatizada e funciona com base num sistema de crédito. A explosão funciona com o melhor esforço e o limite de rutura não é uma garantia, as ações de ficheiropodem rebentar *até* ao limite.

Os créditos acumulam-se num balde de rutura sempre que o tráfego para a sua parte de ficheiro saem abaixo do IOPS de base. Por exemplo, uma quota de 100 GiB tem 100 IOPS de base. Se o tráfego real na parte foi de 40 IOPS para um intervalo específico de 1 segundo, então os 60 IOPS não utilizados são creditados a um balde de rutura. Estes créditos serão então utilizados posteriormente quando as operações excederem os IOPs de base.

> [!TIP]
> Tamanho do balde de rutura = IOPS base * 2 * 3600.

Sempre que uma ação exceda o IOPS de base e tenha créditos num balde de rutura, rebenta. As ações podem continuar a rebentar enquanto os créditos permanecerem, embora as ações inferiores a 50 TiB só permaneçam no limite de rutura até uma hora. As ações superiores a 50 TiB podem tecnicamente ultrapassar este limite de uma hora, até duas horas, mas, isto baseia-se no número de créditos de rutura acumulados. Cada IO para além da linha de base IOPS consome um crédito e uma vez consumidos todos os créditos a parte regressaria ao IOPS de base.

Os créditos de ações têm três estados:

- Acumulação, quando a parte do ficheiro está a usar menos do que o IOPS de base.
- Em declínio, quando a parte do ficheiro está a rebentar.
- Permanecendo constante, quando não há créditos ou IOPS de base estão em uso.

As novas ações começam com o número total de créditos no balde de rutura. Os créditos de rebentamento não serão acumulados se o IOPS de ação ficar abaixo do IOPS de base devido à aceleração pelo servidor.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Ativar as ações de ficheiro padrão até 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migração
Em muitos casos, não irá estabelecer uma nova quota de ficheiros líquida para a sua organização, mas sim migrar uma quota de ficheiro existente de um servidor de ficheiros no local ou dispositivo NAS para O Ficheiro Sino. Existem muitas ferramentas, fornecidas tanto pela Microsoft como pela 3ª parte, para fazer uma migração para uma partilha de ficheiros, mas podem aproximadamente ser divididas em duas categorias:

- **Ferramentas que mantêm atributos do sistema de ficheiros tais como ACLs e carimbos**de tempo:
    - **[Sincronização de ficheiros Azure](storage-sync-files-planning.md)** : O Sincronizado de Ficheiros Azure pode ser usado como um método para ingerir dados numa partilha de ficheiros Azure, mesmo quando a implementação final desejada não é para manter uma presença no local. O Azure File Sync pode ser instalado nas implementações existentes do Windows Server 2012 R2, Windows Server 2016 e Windows Server 2019. Uma vantagem para usar o Sync de Ficheiros Azure como um mecanismo ingerir é que os utilizadores finais podem continuar a utilizar a parte de ficheiro existente no lugar; A redução da parte do ficheiro Azure pode ocorrer depois de todos os dados terem terminado o upload em segundo plano.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy é uma ferramenta de cópia bem conhecida que envia com Windows e Windows Server. A robocópia pode ser usada para transferir dados para Ficheiros Azure, montando a parte do ficheiro localmente e, em seguida, usando a localização montada como destino no comando Robocopy.

- **Ferramentas que não mantêm atributos**do sistema de ficheiros:
    - **Data Box**: Data Box fornece um mecanismo offline de transferência de dados para dados físicos do navio para o Azure. Este método foi concebido para aumentar a largura de banda de entrada e poupar, mas atualmente não suporta atributos do sistema de ficheiros como selos temporais e ACLs.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy é um utilitário de linha de comando projetado para copiar dados de e para o Azure Files, bem como armazenamento Azure Blob, utilizando comandos simples com um desempenho ótimo.

## <a name="next-steps"></a>Passos Seguintes
* [Planejamento de uma implantação de sincronização de ficheiros Azure](storage-sync-files-planning.md)
* [Implementação de ficheiros Azure](storage-files-deployment-guide.md)
* [Implementação de sincronização de ficheiros Azure](storage-sync-files-deployment-guide.md)
