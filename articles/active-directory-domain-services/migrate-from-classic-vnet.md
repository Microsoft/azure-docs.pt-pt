---
title: Migrar Azure AD Domain Services de uma rede virtual clássica | Microsoft Docs
description: Saiba como migrar uma instância de domínio gerenciado Azure AD Domain Services existente do modelo de rede virtual clássica para uma rede virtual baseada no Resource Manager.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: iainfou
ms.openlocfilehash: aafefeb94f3b150789a91c3cf669520ccb522dd8
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893064"
---
# <a name="preview---migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Visualização – migre Azure AD Domain Services do modelo de rede virtual clássica para o Gerenciador de recursos

Azure Active Directory Domain Services (AD DS) dá suporte a uma única movimentação para clientes que atualmente usam o modelo de rede virtual clássica para o modelo de rede virtual do Resource Manager.

Este artigo descreve os benefícios e as considerações de migração e as etapas necessárias para migrar com êxito uma instância existente do AD DS do Azure. Esta funcionalidade encontra-se em pré-visualização.

## <a name="overview-of-the-migration-process"></a>Descrição do processo de migração

O processo de migração usa uma instância existente do AD DS do Azure que é executada em uma rede virtual clássica e a move para uma rede virtual do Resource Manager existente. A migração é realizada usando o PowerShell e tem dois estágios principais de *preparação* e *migração*de execução.

![Visão geral do processo de migração para o Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

No estágio de *preparação* , o Azure AD DS faz um backup do domínio para obter o instantâneo mais recente de usuários, grupos e senhas sincronizados com o domínio gerenciado. A sincronização é desabilitada e o serviço de nuvem que hospeda o domínio gerenciado AD DS do Azure é excluído. Durante o estágio de preparação, o domínio gerenciado AD DS do Azure não é capaz de autenticar usuários.

![Estágio de preparação para migrar AD DS do Azure](media/migrate-from-classic-vnet/migration-preparation.png)

No estágio de *migração* , os discos virtuais subjacentes para os controladores de domínio do domínio gerenciado clássico do Azure AD DS são copiados para criar as VMs usando o modelo de implantação do Gerenciador de recursos. Em seguida, o domínio gerenciado do Azure AD DS é recriado, o que inclui os LDAPs e a configuração do DNS. A sincronização com o Azure AD é reiniciada e os certificados LDAP são restaurados. Não é necessário reassociar quaisquer computadores a um domínio gerenciado AD DS do Azure – eles continuam a ingressar no domínio gerenciado e são executados sem alterações.

![Migração do Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Benefícios da migração

Ao mover um domínio gerenciado do Azure AD DS usando esse processo de migração, você evita a necessidade de reingressar computadores no domínio gerenciado ou excluir a instância do AD DS do Azure e criar uma do zero. As VMs continuam a ser unidas ao domínio gerenciado AD DS do Azure no final do processo de migração.

Após a migração, o Azure AD DS fornece muitos recursos que só estão disponíveis para domínios que usam redes virtuais do Resource Manager, como:

* Suporte refinado à política de senha.
* Proteção de bloqueio de conta do AD.
* Notificações por email de alertas no domínio gerenciado AD DS do Azure.
* Logs de auditoria usando Azure Monitor.
* Integração de arquivos do Azure
* Integração de informações de HD

Os domínios gerenciados do Azure AD DS que usam uma rede virtual do Resource Manager ajudam você a se manter atualizado com os novos recursos mais recentes. O suporte para o Azure AD DS usando redes virtuais clássicas é para ser preterido no futuro.

## <a name="example-scenarios-for-migration"></a>Cenários de exemplo de migração

Alguns cenários comuns para migrar um domínio gerenciado do Azure AD DS incluem os exemplos a seguir.

> [!NOTE]
> Não converta a rede virtual clássica até que você tenha confirmado uma migração bem-sucedida. A conversão da rede virtual remove a opção de reverter ou restaurar o domínio gerenciado AD DS do Azure se houver problemas durante os estágios de migração e verificação.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrar AD DS do Azure para uma rede virtual do Resource Manager existente (recomendado)

Um cenário comum é onde você já moveu outros recursos clássicos existentes para um modelo de implantação do Resource Manager e uma rede virtual. Em seguida, o emparelhamento é usado da rede virtual do Resource Manager para a rede virtual clássica que continua a executar o Azure AD DS. Essa abordagem permite que os aplicativos e serviços do Gerenciador de recursos usem a funcionalidade de autenticação e gerenciamento do domínio gerenciado AD DS do Azure na rede virtual clássica. Após a migração, todos os recursos são executados usando o modelo de implantação do Resource Manager e a rede virtual.

![Migrar AD DS do Azure para uma rede virtual do Resource Manager existente](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

As etapas de alto nível envolvidas neste cenário de migração de exemplo incluem as seguintes partes:

1. Remova os gateways de VPN existentes ou o emparelhamento de rede virtual configurado na rede virtual clássica.
1. Migre o domínio gerenciado AD DS do Azure usando as etapas descritas neste artigo.
1. Teste e confirme uma migração bem-sucedida e, em seguida, exclua a rede virtual clássica.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migre vários recursos, incluindo o Azure AD DS

Neste cenário de exemplo, você migra AD DS do Azure e outros recursos associados do modelo de implantação clássico para o modelo de implantação do Gerenciador de recursos. Se alguns recursos continuarem a ser executados na rede virtual clássica juntamente com o domínio gerenciado do Azure AD DS, eles poderão se beneficiar da migração para o modelo de implantação do Resource Manager.

![Migrar vários recursos para o modelo de implantação do Gerenciador de recursos](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

As etapas de alto nível envolvidas neste cenário de migração de exemplo incluem as seguintes partes:

1. Remova os gateways de VPN existentes ou o emparelhamento de rede virtual configurado na rede virtual clássica.
1. Migre o domínio gerenciado AD DS do Azure usando as etapas descritas neste artigo.
1. Configure o emparelhamento de rede virtual entre a rede virtual clássica e a rede do Gerenciador de recursos.
1. Teste e confirme uma migração bem-sucedida.
1. [Mova recursos clássicos adicionais como VMs][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrar AD DS do Azure, mas manter outros recursos na rede virtual clássica

Com este cenário de exemplo, você tem a quantidade mínima de tempo de inatividade em uma sessão. Você só migra AD DS do Azure para uma rede virtual do Resource Manager e mantém os recursos existentes no modelo de implantação clássico e na rede virtual. Em um período de manutenção seguinte, você pode migrar os recursos adicionais do modelo de implantação clássico e da rede virtual conforme desejado.

![Migrar somente AD DS do Azure para o modelo de implantação do Gerenciador de recursos](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

As etapas de alto nível envolvidas neste cenário de migração de exemplo incluem as seguintes partes:

1. Remova os gateways de VPN existentes ou o emparelhamento de rede virtual configurado na rede virtual clássica.
1. Migre o domínio gerenciado AD DS do Azure usando as etapas descritas neste artigo.
1. Configure o emparelhamento de rede virtual entre a rede virtual clássica e a nova rede virtual do Gerenciador de recursos.
1. Posteriormente, [migre os recursos adicionais][migrate-iaas] da rede virtual clássica, conforme necessário.

## <a name="before-you-begin"></a>Antes de começar

Ao preparar e migrar um domínio gerenciado do Azure AD DS, há algumas considerações sobre a disponibilidade dos serviços de autenticação e gerenciamento. O domínio gerenciado do AD DS do Azure não está disponível por um período de tempo durante a migração. Aplicativos e serviços que dependem do Azure AD DS apresentam tempo de inatividade durante a migração.

> [!IMPORTANT]
> Leia todo este artigo de migração e diretrizes antes de iniciar o processo de migração. O processo de migração afeta a disponibilidade dos controladores de domínio do Azure AD DS por períodos de tempo. Os usuários, serviços e aplicativos não podem se autenticar no domínio gerenciado durante o processo de migração.

### <a name="ip-addresses"></a>Endereços IP

Os endereços IP do controlador de domínio para um domínio gerenciado do Azure AD DS são alterados após a migração. Isso inclui o endereço IP público para o ponto de extremidade LDAP seguro. Os novos endereços IP estão dentro do intervalo de endereços para a nova sub-rede na rede virtual do Resource Manager.

No caso da reversão, os endereços IP podem mudar após a reversão.

O Azure AD DS normalmente usa os dois primeiros endereços IP disponíveis no intervalo de endereços, mas isso não é garantido. No momento, não é possível especificar os endereços IP a serem usados após a migração.

### <a name="downtime"></a>Inatividade

O processo de migração envolve os controladores de domínio que estão offline por um período de tempo. Os controladores de domínio ficam inacessíveis enquanto o Azure AD DS é migrado para o modelo de implantação do Gerenciador de recursos e para a rede virtual. Em média, o tempo de inatividade é de cerca de 1 a 3 horas. Esse período de tempo é de quando os controladores de domínio são colocados offline no momento em que o primeiro controlador de domínio volta a ficar online. Essa média não inclui o tempo que leva para o segundo controlador de domínio ser replicado ou o tempo que pode levar para migrar recursos adicionais para o modelo de implantação do Gerenciador de recursos.

### <a name="account-lockout"></a>Bloqueio de conta

O Azure AD DS domínios gerenciados que são executados em redes virtuais clássicas não têm políticas de bloqueio de conta do AD em vigor. Se as VMs forem expostas à Internet, os invasores poderão usar métodos de irrigação de senha para forçar sua maneira de fazer isso em contas. Não há nenhuma política de bloqueio de conta para interromper essas tentativas. Para os domínios gerenciados do Azure AD DS que usam o modelo de implantação do Resource Manager e as redes virtuais, as políticas de bloqueio de conta do AD protegem contra esses ataques de irrigação de senha.

Por padrão, 5 tentativas de senha inadequadas em 2 minutos bloqueiam uma conta por 30 minutos.

Uma conta bloqueada não pode ser conectada ao, o que pode interferir na capacidade de gerenciar o domínio gerenciado do Azure AD DS ou os aplicativos gerenciados pela conta. Depois que um domínio gerenciado do Azure AD DS é migrado, as contas podem ter o que se parece com um bloqueio permanente devido a tentativas com falha repetidas para entrar. Dois cenários comuns após a migração incluem o seguinte:

* Uma conta de serviço que está usando uma senha expirada.
    * A conta de serviço tenta repetidamente entrar com uma senha expirada, o que bloqueia a conta. Para corrigir isso, localize o aplicativo ou a VM com credenciais expiradas e atualize a senha.
* Uma entidade mal-intencionada está usando tentativas de força bruta para entrar em contas.
    * Quando as VMs são expostas à Internet, os invasores geralmente tentam combinações comuns de nome de usuário e senha à medida que tentam assinar. Essas tentativas de entrada com falha repetidas podem bloquear as contas. Não é recomendável usar contas de administrador com nomes genéricos, como *admin* ou *administrador*, por exemplo, para minimizar as contas administrativas de serem bloqueadas.
    * Minimize o número de VMs que são expostas à Internet. Você pode usar a [bastiões do Azure (atualmente em visualização)][azure-bastion] para se conectar com segurança às VMs usando o portal do Azure.

Se você suspeitar que algumas contas podem ser bloqueadas após a migração, as etapas finais de migração descrevem como habilitar a auditoria ou alterar as configurações de política de senha refinadas.

### <a name="roll-back-and-restore"></a>Reverter e restaurar

Se a migração não for bem-sucedida, haverá processo para reverter ou restaurar um domínio gerenciado do Azure AD DS. ROLLBACK é uma opção de autoatendimento para retornar imediatamente o estado do domínio gerenciado para antes da tentativa de migração. Os engenheiros de suporte do Azure também podem restaurar um domínio gerenciado do backup como um último recurso. Para obter mais informações, consulte [como reverter ou restaurar de uma migração com falha](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Restrições em redes virtuais disponíveis

Há algumas restrições nas redes virtuais para as quais um domínio gerenciado do Azure AD DS pode ser migrado. A rede virtual do Gerenciador de recursos de destino deve atender aos seguintes requisitos:

* A rede virtual do Resource Manager deve estar na mesma assinatura do Azure que a rede virtual clássica na qual o Azure AD DS está implantado no momento.
* A rede virtual do Resource Manager deve estar na mesma região que a rede virtual clássica na qual o Azure AD DS está implantado no momento.
* A sub-rede da rede virtual do Gerenciador de recursos deve ter pelo menos 3-5 endereços IP disponíveis.
* A sub-rede da rede virtual do Resource Manager deve ser uma sub-rede dedicada para o Azure AD DS e não deve hospedar nenhuma outra carga de trabalho.

Para obter mais informações sobre os requisitos de rede virtual, consulte [considerações de design de rede virtual e opções de configuração][network-considerations].

## <a name="migration-steps"></a>Passos de migração

A migração para o modelo de implantação do Gerenciador de recursos e a rede virtual é dividida em cinco etapas principais:

| Passo    | Executado por meio de  | Tempo estimado  | Inatividade  | Reverter/restaurar? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Etapa 1-atualizar e localizar a nova rede virtual](#update-and-verify-virtual-network-settings) | Portal do Azure | 15 minutos | Nenhum tempo de inatividade necessário | N/A |
| [Etapa 2 – preparar o domínio gerenciado AD DS do Azure para migração](#prepare-the-managed-domain-for-migration) | PowerShell | 15 a 30 minutos em média | O tempo de inatividade da AD DS do Azure é iniciado após a conclusão desse comando. | Reversão e restauração disponíveis. |
| [Etapa 3 – mover o domínio gerenciado do Azure AD DS para uma rede virtual existente](#migrate-the-managed-domain) | PowerShell | 1 a 3 horas em média | Um controlador de domínio está disponível quando esse comando é concluído, o tempo de inatividade termina. | Em caso de falha, a reversão (autoatendimento) e a restauração estão disponíveis. |
| [Etapa 4 – testar e aguardar o controlador de domínio de réplica](#test-and-verify-connectivity-after-the-migration)| PowerShell e portal do Azure | 1 hora ou mais, dependendo do número de testes | Ambos os controladores de domínio estão disponíveis e devem funcionar normalmente. | N/D. Depois que a primeira VM for migrada com êxito, não há nenhuma opção para reversão ou restauração. |
| [Etapa 5-etapas de configuração opcionais](#optional-post-migration-configuration-steps) | portal do Azure e VMs | N/A | Nenhum tempo de inatividade necessário | N/A |

> [!IMPORTANT]
> Para evitar tempo de inatividade adicional, leia todo este artigo de migração e diretrizes antes de iniciar o processo de migração. O processo de migração afeta a disponibilidade dos controladores de domínio do Azure AD DS por um período de tempo. Os usuários, serviços e aplicativos não podem se autenticar no domínio gerenciado durante o processo de migração.

## <a name="update-and-verify-virtual-network-settings"></a>Atualizar e verificar as configurações de rede virtual

Antes de iniciar a migração, conclua as seguintes verificações e atualizações iniciais. Essas etapas podem acontecer a qualquer momento antes da migração e não afetam a operação do domínio gerenciado AD DS do Azure.

1. Atualize seu ambiente de Azure PowerShell local para a versão mais recente. Para concluir as etapas de migração, você precisa de pelo menos a versão *2.3.2*.

    Para obter informações sobre como verificar e atualizar, consulte [Azure PowerShell visão geral][azure-powershell].

1. Crie ou escolha uma rede virtual existente do Resource Manager.

    Verifique se as configurações de rede não bloqueiam as portas necessárias necessárias para o AD DS do Azure. As portas devem ser abertas na rede virtual clássica e na rede virtual do Resource Manager. Essas configurações incluem tabelas de rotas (embora não seja recomendável usar tabelas de rotas) e grupos de segurança de rede.

    Para exibir as portas necessárias, consulte [grupos de segurança de rede e portas necessárias][network-ports]. Para minimizar problemas de comunicação de rede, é recomendável aguardar e aplicar um grupo de segurança de rede ou uma tabela de rotas à rede virtual do Resource Manager após a migração ser concluída com êxito.

    Anote esse grupo de recursos de destino, a rede virtual de destino e a sub-rede de rede virtual de destino. Esses nomes de recursos são usados durante o processo de migração.

1. Verifique a integridade do domínio gerenciado do Azure AD DS no portal do Azure. Se você tiver algum alerta para o domínio gerenciado, resolva-os antes de iniciar o processo de migração.
1. Opcionalmente, se você planeja mover outros recursos para o modelo de implantação do Resource Manager e para a rede virtual, confirme se esses recursos podem ser migrados. Para obter mais informações, consulte [migração de recursos de IaaS com suporte da plataforma do clássico para o Gerenciador de recursos][migrate-iaas].

    > [!NOTE]
    > Não converta a rede virtual clássica em uma rede virtual do Resource Manager. Se você fizer isso, não haverá opção para reverter ou restaurar o domínio gerenciado AD DS do Azure.

## <a name="prepare-the-managed-domain-for-migration"></a>Preparar o domínio gerenciado para migração

Azure PowerShell é usado para preparar o domínio gerenciado do Azure AD DS para migração. Essas etapas incluem fazer um backup, pausar a sincronização e excluir o serviço de nuvem que hospeda o Azure AD DS. Quando essa etapa for concluída, o Azure AD DS será colocado offline por um período de tempo. Se a etapa de preparação falhar, você poderá [reverter para o estado anterior](#roll-back).

Para preparar o domínio gerenciado AD DS do Azure para migração, conclua as seguintes etapas:

1. Instale o script de `Migrate-Aaads` do [Galeria do PowerShell][powershell-script]. Esse script de migração do PowerShell é assinado digitalmente pela equipe de engenharia do Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Crie uma variável para manter as credenciais para o script de migração usando o cmdlet [Get-Credential][get-credential] .

    A conta de usuário especificada precisa de privilégios de *administrador global* em seu locatário do Azure ad para habilitar o Azure AD DS e, em seguida, privilégios de *colaborador* em sua assinatura do Azure para criar os recursos de AD DS do Azure necessários.

    Quando solicitado, insira uma conta de usuário e senha apropriadas:

    ```powershell
    $creds = Get-Credential
    ```

1. Agora, execute o cmdlet `Migrate-Aadds` usando o parâmetro *-Prepare* . Forneça o *-ManagedDomainFqdn* para seu próprio domínio gerenciado AD DS do Azure, como *contoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare -ManagedDomainFqdn contoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Migrar o domínio gerenciado

Com o domínio gerenciado AD DS do Azure preparado e submetido a backup, o domínio pode ser migrado. Esta etapa recria as VMs do controlador de domínio Azure AD Domain Services usando o modelo de implantação do Gerenciador de recursos. Esta etapa pode levar de 1 a 3 horas para ser concluída.

Execute o cmdlet `Migrate-Aadds` usando o parâmetro *-Commit* . Forneça o *-ManagedDomainFqdn* para seu próprio domínio gerenciado AD DS do Azure preparado na seção anterior, como *contoso.com*:

Especifique o grupo de recursos de destino que contém a rede virtual para a qual você deseja migrar AD DS do Azure, como *MyResource*Group. Forneça a rede virtual de destino, como *myVnet*, e a sub-rede, como *DomainServices*.

Depois que esse comando for executado, você não poderá reverter:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn contoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Depois que o script validar que o domínio gerenciado está preparado para migração, digite *Y* para iniciar o processo de migração.

> [!IMPORTANT]
> Não converta a rede virtual clássica em uma rede virtual do Resource Manager durante o processo de migração. Se você converter a rede virtual, não poderá reverter ou restaurar o domínio gerenciado AD DS do Azure, já que a rede virtual original não existirá mais.

A cada dois minutos durante o processo de migração, um indicador de progresso informa o status atual, conforme mostrado na seguinte saída de exemplo:

![Indicador de progresso da migração do Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

O processo de migração continua a ser executado, mesmo se você fechar o script do PowerShell. No portal do Azure, o status do domínio gerenciado é reportado como *migrando*.

Quando a migração for concluída com êxito, você poderá exibir o endereço IP do seu primeiro controlador de domínio no portal do Azure ou por meio de Azure PowerShell. Uma estimativa de tempo no segundo controlador de domínio que está sendo disponibilizado também é mostrada.

Neste estágio, você pode opcionalmente mover outros recursos existentes do modelo de implantação clássico e da rede virtual. Ou você pode manter os recursos no modelo de implantação clássico e emparelhar as redes virtuais entre si depois que a migração de AD DS do Azure for concluída.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testar e verificar a conectividade após a migração

Pode levar algum tempo para que o segundo controlador de domínio seja implantado com êxito e esteja disponível para uso no domínio gerenciado AD DS do Azure.

Com o modelo de implantação do Gerenciador de recursos, os recursos de rede para o domínio gerenciado do AD DS do Azure são mostrados no portal do Azure ou Azure PowerShell. Para saber mais sobre o que esses recursos de rede são e fazem, consulte [recursos de rede usados pelo Azure AD DS][network-resources].

Quando pelo menos um controlador de domínio estiver disponível, conclua as seguintes etapas de configuração para conectividade de rede com VMs:

* **Atualizar configurações do servidor DNS** Para permitir que outros recursos na rede virtual do Resource Manager resolvam e usem o domínio gerenciado AD DS do Azure, atualize as configurações de DNS com os endereços IP dos novos controladores de domínio. O portal do Azure pode configurar automaticamente essas configurações para você. Para saber mais sobre como configurar a rede virtual do Resource Manager, consulte [Atualizar configurações de DNS para a rede virtual do Azure][update-dns].
* **Reiniciar VMs ingressadas no domínio** -como os endereços IP do servidor DNS para os controladores de domínio do AD DS do Azure mudam, reinicie qualquer VM ingressada no domínio para que elas usem as novas configurações do servidor DNS. Se os aplicativos ou as VMs tiverem configurado manualmente as configurações de DNS, atualize-as manualmente com os novos endereços IP do servidor DNS dos controladores de domínio mostrados no portal do Azure.

Agora, teste a conexão de rede virtual e a resolução de nomes. Em uma VM que está conectada à rede virtual do Resource Manager ou emparelhada a ela, experimente os seguintes testes de comunicação de rede:

1. Verifique se você pode executar o ping no endereço IP de um dos controladores de domínio, como `ping 10.1.0.4`
    * Os endereços IP dos controladores de domínio são mostrados na página **Propriedades** do domínio gerenciado AD DS do Azure no portal do Azure.
1. Verificar a resolução de nomes do domínio gerenciado, como `nslookup contoso.com`
    * Especifique o nome DNS para seu próprio domínio gerenciado AD DS do Azure para verificar se as configurações de DNS estão corretas e resolvidas.

O segundo controlador de domínio deve estar disponível 1-2 horas após a conclusão do cmdlet de migração. Para verificar se o segundo controlador de domínio está disponível, examine a página de **Propriedades** do domínio gerenciado AD DS do Azure na portal do Azure. Se dois endereços IP forem mostrados, o segundo controlador de domínio estará pronto.

## <a name="optional-post-migration-configuration-steps"></a>Etapas opcionais de configuração após a migração

Quando o processo de migração é concluído com êxito, algumas etapas opcionais de configuração incluem a habilitação de logs de auditoria ou notificações por email ou a atualização da política de senha refinada.

#### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Assinar logs de auditoria usando Azure Monitor

O Azure AD DS expõe logs de auditoria para ajudar a solucionar problemas e exibir eventos nos controladores de domínio. Para obter mais informações, consulte [habilitar e usar logs de auditoria][security-audits].

Você pode usar modelos para monitorar informações importantes expostas nos logs. Por exemplo, o modelo de pasta de trabalho log de auditoria pode monitorar possíveis bloqueios de conta no domínio gerenciado AD DS do Azure.

#### <a name="configure-azure-ad-domain-services-email-notifications"></a>Configurar Azure AD Domain Services notificações por email

Para ser notificado quando um problema for detectado no domínio gerenciado AD DS do Azure, atualize as configurações de notificação de email no portal do Azure. Para obter mais informações, consulte [definir configurações de notificação][notifications].

#### <a name="update-fine-grained-password-policy"></a>Atualizar política de senha refinada

Se necessário, você pode atualizar a política de senha refinada para ser menos restritiva do que a configuração padrão. Você pode usar os logs de auditoria para determinar se uma configuração menos restritiva faz sentido e, em seguida, configurar a política conforme necessário. Use as seguintes etapas de alto nível para revisar e atualizar as configurações de política para contas que são bloqueadas repetidamente após a migração:

1. [Configure a política de senha][password-policy] para menos restrições no domínio gerenciado AD DS do Azure e observe os eventos nos logs de auditoria.
1. Se qualquer conta de serviço estiver usando senhas expiradas, conforme identificado nos logs de auditoria, atualize essas contas com a senha correta.
1. Se a VM estiver exposta à Internet, examine nomes de conta genérica como *administrador*, *usuário*ou *convidado* com tentativas de entrada altas. Sempre que possível, atualize essas VMs para usar contas com nomes menos genéricos.
1. Use um rastreamento de rede na VM para localizar a origem dos ataques e bloquear esses endereços IP para poder tentar entrar.
1. Quando houver problemas mínimos de bloqueio, atualize a política de senha refinada para ser tão restritiva quanto necessário.

#### <a name="creating-a-network-security-group"></a>Criar um grupo de segurança de rede

O Azure AD DS precisa de um grupo de segurança de rede para proteger as portas necessárias para o domínio gerenciado e bloquear todo o tráfego de entrada. Esse grupo de segurança de rede atua como uma camada extra de proteção para bloquear o acesso ao domínio gerenciado e não é criado automaticamente. Para criar o grupo de segurança de rede e abrir as portas necessárias, examine as seguintes etapas:

1. Na portal do Azure, selecione o recurso de AD DS do Azure. Na página Visão geral, um botão é exibido para criar um grupo de segurança de rede se não houver nenhum associado a Azure AD Domain Services.
1. Se você usar LDAP seguro, adicione uma regra ao grupo de segurança de rede para permitir o tráfego de entrada para a porta *TCP* *636*. Para obter mais informações, consulte [Configurar o LDAP seguro][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Reverter e restaurar da migração

### <a name="roll-back"></a>Reverter

Se houver um erro quando você executar o cmdlet do PowerShell para se preparar para a migração na etapa 2 ou para a migração em si na etapa 3, o domínio gerenciado do Azure AD DS poderá reverter para a configuração original. Essa reversão requer a rede virtual clássica original. Observe que os endereços IP ainda podem ser alterados após a reversão.

Execute o cmdlet `Migrate-Aadds` usando o parâmetro *-Abort* . Forneça o *-ManagedDomainFqdn* para seu próprio domínio gerenciado AD DS do Azure preparado em uma seção anterior, como *contoso.com*e o nome da rede virtual clássica, como *myClassicVnet*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn contoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Restauro

Como último recurso, Azure AD Domain Services pode ser restaurado do último backup disponível. Um backup é feito na etapa 1 da migração para garantir que o backup mais recente esteja disponível. Esse backup é armazenado por 30 dias.

Para restaurar o domínio gerenciado do Azure AD DS do backup, [abra um tíquete de caso de suporte usando o portal do Azure][azure-support]. Forneça a ID do diretório, o nome de domínio e o motivo da restauração. O processo de suporte e restauração pode levar vários dias para ser concluído.

## <a name="troubleshooting"></a>Resolução de problemas

Se você tiver problemas após a migração para o modelo de implantação do Gerenciador de recursos, examine algumas das seguintes áreas comuns de solução de problemas:

* [Solucionar problemas de ingresso no domínio][troubleshoot-domain-join]
* [Solucionar problemas de bloqueio de conta][troubleshoot-account-lockout]
* [Solucionar problemas de conexão de conta][troubleshoot-sign-in]
* [Solucionar problemas de conectividade LDAP segura][tshoot-ldaps]

## <a name="next-steps"></a>Passos seguintes

Com o domínio gerenciado do Azure AD DS migrado para o modelo de implantação do Gerenciador de recursos, [crie e ingresse no domínio de uma VM do Windows][join-windows] e, em seguida, [Instale as ferramentas de gerenciamento][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
