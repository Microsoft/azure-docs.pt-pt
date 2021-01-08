---
title: Serviços de Domínio Azure Azure migram de uma rede virtual clássica Microsoft Docs
description: Saiba como migrar um domínio gerido pelos Serviços de Domínio Azure AD existentes do modelo de rede virtual Clássico para uma rede virtual baseada em Gestores de Recursos.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2020
ms.author: justinha
ms.openlocfilehash: 694ed5304e838057141b7df043565d58188fc870
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013044"
---
# <a name="migrate-azure-active-directory-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Migrar serviços de domínio do diretório ativo do Azure do modelo de rede virtual clássico para o Gestor de Recursos

O Azure Ative Directory Domain Services (Azure AD DS) suporta um movimento único para os clientes que atualmente utilizam o modelo de rede virtual Clássico para o modelo de rede virtual Do Gestor de Recursos. Os domínios geridos pela Azure AD DS que utilizam o modelo de implementação do Gestor de Recursos fornecem funcionalidades adicionais, tais como a política de palavras-passe de grãos finos, registos de auditoria e proteção de bloqueio de conta.

Este artigo descreve considerações para a migração, em seguida, as medidas necessárias para migrar com sucesso um domínio gerido existente. Para alguns dos benefícios, consulte [os benefícios da migração do modelo de implementação Classic to Resource Manager em Azure AD DS][migration-benefits].

> [!NOTE]
> Em 2017, a Azure AD Domain Services tornou-se disponível para acolher numa rede Azure Resource Manager. Desde então, conseguimos construir um serviço mais seguro utilizando as capacidades modernas do Gestor de Recursos Azure. Como as implementações do Azure Resource Manager substituem totalmente as implementações clássicas, as implementações clássicas da rede virtual Azure AD DS serão retiradas a 1 de março de 2023.
>
> Para mais informações, consulte o [aviso oficial de depreciação.](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>Visão geral do processo de migração

O processo de migração requer um domínio gerido existente que funciona numa rede virtual Clássica e o transfere para uma rede virtual do Gestor de Recursos existente. A migração é realizada com recurso ao PowerShell, e tem duas fases principais de execução: *preparação* e *migração.*

![Visão geral do processo de migração para Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

Na fase *de preparação,* o Azure AD DS requer uma cópia de segurança do domínio para obter a mais recente imagem de utilizadores, grupos e palavras-passe sincronizadas ao domínio gerido. A sincronização é então desativada e o serviço de nuvem que acolhe o domínio gerido é eliminado. Durante a fase de preparação, o domínio gerido não consegue autenticar os utilizadores.

![Fase de preparação para a migração da Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

Na fase de *migração,* os discos virtuais subjacentes aos controladores de domínio do domínio gerido clássico são copiados para criar os VMs utilizando o modelo de implementação do Gestor de Recursos. O domínio gerido é então recriado, que inclui a configuração LDAPS e DNS. A sincronização para Azure AD é reiniciada e os certificados LDAP são restaurados. Não há necessidade de juntar nenhuma máquina a um domínio gerido – elas continuam a ser unidas ao domínio gerido e funcionam sem alterações.

![Migração de Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="example-scenarios-for-migration"></a>Cenários de exemplo para a migração

Alguns cenários comuns para a migração de um domínio gerido incluem os seguintes exemplos.

> [!NOTE]
> Não converta a rede virtual Classic até ter confirmado uma migração bem sucedida. A conversão da rede virtual remove a opção de retrocedar ou restaurar o domínio gerido se houver algum problema durante as fases de migração e verificação.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrar Azure AD DS para uma rede virtual do Gestor de Recursos existente (recomendado)

Um cenário comum é onde já transferiu outros recursos clássicos existentes para um modelo de implementação de Gestor de Recursos e rede virtual. O peering é então usado da rede virtual Resource Manager para a rede virtual Classic que continua a executar Azure AD DS. Esta abordagem permite que as aplicações e serviços do Gestor de Recursos utilizem a autenticação e funcionalidade de gestão do domínio gerido na rede virtual Classic. Uma vez migrados, todos os recursos são executados utilizando o modelo de implementação do Gestor de Recursos e a rede virtual.

![Migrar Azure AD DS para uma rede virtual do Gestor de Recursos existente](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

As etapas de alto nível envolvidas neste cenário de migração de exemplo incluem as seguintes partes:

1. Remova as portas VPN existentes ou o espreitamento de rede virtual configurado na rede virtual Classic.
1. Migrar o domínio gerido usando os passos descritos neste artigo.
1. Teste e confirme uma migração bem sucedida e, em seguida, elimine a rede virtual Classic.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migrar vários recursos, incluindo Azure AD DS

Neste cenário de exemplo, migra o Azure AD DS e outros recursos associados do modelo de implementação Clássico para o modelo de implementação do Gestor de Recursos. Se alguns recursos continuarem a funcionar na rede virtual Clássica ao lado do domínio gerido, todos eles podem beneficiar da migração para o modelo de implementação do Gestor de Recursos.

![Migrar vários recursos para o modelo de implementação do Gestor de Recursos](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

As etapas de alto nível envolvidas neste cenário de migração de exemplo incluem as seguintes partes:

1. Remova as portas VPN existentes ou o espreitamento de rede virtual configurado na rede virtual Classic.
1. Migrar o domínio gerido usando os passos descritos neste artigo.
1. Configurar a rede virtual que espreita entre a rede virtual Clássica e a rede Resource Manager.
1. Teste e confirme uma migração bem sucedida.
1. [Mover recursos clássicos adicionais como VMs][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrar Azure AD DS mas manter outros recursos na rede virtual Clássica

Com este cenário de exemplo, você tem o mínimo de tempo de inatividade em uma sessão. Você só migra Azure AD DS para uma rede virtual Gestor de Recursos, e mantém os recursos existentes no modelo de implementação clássico e rede virtual. Num período de manutenção seguinte, pode migrar os recursos adicionais do modelo de implementação Clássico e da rede virtual conforme desejado.

![Migrar apenas Azure AD DS para o modelo de implementação do Gestor de Recursos](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

As etapas de alto nível envolvidas neste cenário de migração de exemplo incluem as seguintes partes:

1. Remova as portas VPN existentes ou o espreitamento de rede virtual configurado na rede virtual Classic.
1. Migrar o domínio gerido usando os passos descritos neste artigo.
1. Crie uma rede virtual que espreita entre a rede virtual Clássica e a nova rede virtual Do Gestor de Recursos.
1. Mais tarde, [migrar os recursos adicionais][migrate-iaas] da rede virtual Clássica, conforme necessário.

## <a name="before-you-begin"></a>Antes de começar

À medida que prepara e migra um domínio gerido, existem algumas considerações em torno da disponibilidade de serviços de autenticação e gestão. O domínio gerido não está disponível durante um período de tempo durante a migração. As aplicações e serviços que dependem do Azure AD DS experimentam tempo de inatividade durante a migração.

> [!IMPORTANT]
> Leia todo este artigo de migração e orientação antes de iniciar o processo de migração. O processo de migração afeta a disponibilidade dos controladores de domínio Azure AD DS por períodos de tempo. Os utilizadores, serviços e aplicações não podem autenticar-se contra o domínio gerido durante o processo de migração.

### <a name="ip-addresses"></a>Endereços IP

O controlador de domínio endereços IP para uma mudança de domínio gerida após a migração. Esta alteração inclui o endereço IP público para o ponto final LDAP seguro. Os novos endereços IP estão dentro da gama de endereços para a nova sub-rede na rede virtual Do Gestor de Recursos.

Se precisar de reverter, os endereços IP podem ser alterados após o reversão.

O Azure AD DS normalmente utiliza os dois primeiros endereços IP disponíveis na gama de endereços, mas isso não está garantido. Não é possível especificar os endereços IP para utilizar após a migração.

### <a name="downtime"></a>Tempo de inatividade

O processo de migração envolve que os controladores de domínio estão offline por um período de tempo. Os controladores de domínio são inacessíveis enquanto o Azure AD DS é migrado para o modelo de implementação do Gestor de Recursos e rede virtual.

Em média, o tempo de inatividade é de cerca de 1 a 3 horas. Este período de tempo é de quando os controladores de domínio são desligados até ao momento em que o primeiro controlador de domínio volta a estar online. Esta média não inclui o tempo que o segundo controlador de domínio leva para replicar, ou o tempo que pode levar para migrar recursos adicionais para o modelo de implementação do Gestor de Recursos.

### <a name="account-lockout"></a>Bloqueio de conta

Os domínios geridos que funcionam em redes virtuais clássicas não têm políticas de bloqueio de conta AD em vigor. Se os VMs forem expostos à internet, os atacantes podem usar métodos de spray de palavra-passe para forçar a entrada bruta nas contas. Não há nenhuma política de bloqueio de contas para parar essas tentativas. Para domínios geridos que utilizam o modelo de implementação do Gestor de Recursos e redes virtuais, as políticas de bloqueio de conta AD protegem contra estes ataques de spray de palavras-passe.

Por predefinição, 5 tentativas de senha estragada em 2 minutos bloqueiam uma conta durante 30 minutos.

Uma conta bloqueada não pode ser usada para iniciar scontabilidade, o que pode interferir com a capacidade de gerir o domínio gerido ou aplicações geridas pela conta. Depois de um domínio gerido ser migrado, as contas podem experimentar o que parece ser um bloqueio permanente devido a repetidas tentativas falhadas de se inscrever. Dois cenários comuns após a migração incluem:

* Uma conta de serviço que está a usar uma senha expirada.
    * A conta de serviço tenta repetidamente iniciar seduca com uma senha expirada, que bloqueia a conta. Para corrigir isto, localize a aplicação ou VM com credenciais caducadas e atualize a palavra-passe.
* Uma entidade maliciosa está a usar tentativas brutas de entrar nas contas.
    * Quando os VMs são expostos à internet, os atacantes muitas vezes experimentam o nome de utilizador comum e as combinações de palavras-passe enquanto tentam assinar. Estas tentativas de inscrição falhadas repetidas podem bloquear as contas. Não é aconselhável utilizar contas de administrador com nomes genéricos como *administração* ou *administrador*, por exemplo, para minimizar as contas administrativas de estarem bloqueadas.
    * Minimize o número de VMs que estão expostos à internet. Pode utilizar [o Azure Bastion][azure-bastion] para ligar-se de forma segura aos VMs utilizando o portal Azure.

Se suspeitar que algumas contas podem ser bloqueadas após a migração, os passos finais de migração descrevem como permitir a auditoria ou alterar as definições de política de palavras-passe de grão fino.

### <a name="roll-back-and-restore"></a>Recue e restaure

Se a migração não for bem sucedida, há processo para retrocedar ou restaurar um domínio gerido. O revés é uma opção de autosserviço para devolver imediatamente o estado do domínio gerido antes da tentativa de migração. Os engenheiros de suporte Azure também podem restaurar um domínio gerido a partir de backup como último recurso. Para obter mais informações, consulte [como retroceda ou restaure a partir de uma migração falhada.](#roll-back-and-restore-from-migration)

### <a name="restrictions-on-available-virtual-networks"></a>Restrições às redes virtuais disponíveis

Existem algumas restrições nas redes virtuais para as quais um domínio gerido pode ser migrado. A rede virtual Destination Resource Manager deve satisfazer os seguintes requisitos:

* A rede virtual Do Gestor de Recursos deve estar na mesma subscrição do Azure que a rede virtual Clássica em que o Azure AD DS está atualmente implantado.
* A rede virtual Do Gestor de Recursos deve estar na mesma região que a rede virtual Clássica em que o Azure AD DS está atualmente implantado.
* A sub-rede virtual do Gestor de Recursos deve ter pelo menos 3-5 endereços IP disponíveis.
* A sub-rede virtual do Gestor de Recursos deve ser uma sub-rede dedicada para Azure AD DS, e não deve acolher quaisquer outras cargas de trabalho.

Para obter mais informações sobre os requisitos de rede virtual, consulte [considerações de design de rede virtual e opções de configuração][network-considerations].

Também deve criar um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerido. Um equilibrador de carga padrão Azure é criado durante o processo de migração que exige que estas regras sejam implementadas. Este grupo de segurança de rede assegura o Azure AD DS e é necessário para que o domínio gerido funcione corretamente.

Para obter mais informações sobre as regras necessárias, consulte [os grupos de segurança da rede Azure AD DS e os portos necessários](network-considerations.md#network-security-groups-and-required-ports).

### <a name="ldaps-and-tlsssl-certificate-expiration"></a>Expiração do certificado LDAPS e TLS/SSL

Se o seu domínio gerido estiver configurado para LDAPS, confirme que o seu certificado TLS/SSL atual é válido por mais de 30 dias. Um certificado que expira nos próximos 30 dias faz com que os processos de migração falhem. Se necessário, renove o certificado e aplique-o no seu domínio gerido, em seguida, inicie o processo de migração.

## <a name="migration-steps"></a>Passos de migração

A migração para o modelo de implementação do Gestor de Recursos e rede virtual divide-se em 5 passos principais:

| Passo    | Realizado através  | Tempo estimado  | Tempo de inatividade  | Voltar/Restaurar? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Passo 1 - Atualizar e localizar a nova rede virtual](#update-and-verify-virtual-network-settings) | Portal do Azure | 15 minutos | Não é necessário tempo de inatividade | N/D |
| [Passo 2 - Preparar o domínio gerido para a migração](#prepare-the-managed-domain-for-migration) | PowerShell | 15 - 30 minutos em média | O tempo de inatividade do Azure AD DS começa após a conclusão deste comando. | Recue e restaure disponível. |
| [Passo 3 - Mover o domínio gerido para uma rede virtual existente](#migrate-the-managed-domain) | PowerShell | 1 - 3 horas, em média | Um controlador de domínio está disponível assim que este comando estiver concluído. | No caso de falha, tanto o reversão (self-service) como o restauro estão disponíveis. |
| [Passo 4 - Teste e aguarde o controlador de domínio de réplica](#test-and-verify-connectivity-after-the-migration)| Portal PowerShell e Azure | 1 hora ou mais, dependendo do número de testes | Ambos os controladores de domínio estão disponíveis e devem funcionar normalmente, terminações de tempo de inatividade. | N/D. Uma vez que o primeiro VM é migrado com sucesso, não há opção para reversão ou restauro. |
| [Passo 5 - Etapas de configuração opcionais](#optional-post-migration-configuration-steps) | Portal Azure e VMs | N/D | Não é necessário tempo de inatividade | N/D |

> [!IMPORTANT]
> Para evitar tempo de inatividade adicional, leia todo este artigo de migração e orientação antes de iniciar o processo de migração. O processo de migração afeta a disponibilidade dos controladores de domínio Azure AD DS por um período de tempo. Os utilizadores, serviços e aplicações não podem autenticar-se contra o domínio gerido durante o processo de migração.

## <a name="update-and-verify-virtual-network-settings"></a>Atualizar e verificar definições de rede virtual

Antes de iniciar o processo de migração, complete as seguintes verificações e atualizações iniciais. Estes passos podem acontecer a qualquer momento antes da migração e não afetam o funcionamento do domínio gerido.

1. Atualize o ambiente Azure PowerShell local para a versão mais recente. Para completar os passos de migração, é necessário pelo menos a versão *2.3.2*.

    Para obter informações sobre como verificar e atualizar a sua versão PowerShell, consulte [a visão geral do Azure PowerShell][azure-powershell].

1. Criar ou escolher uma rede virtual existente, Gestor de Recursos.

    Certifique-se de que as definições de rede não bloqueiam as portas necessárias para o Azure AD DS. As portas devem estar abertas tanto na rede virtual Clássica como na rede virtual Do Gestor de Recursos. Estas definições incluem tabelas de rotas (embora não seja recomendado usar tabelas de rota) e grupos de segurança de rede.

    O Azure AD DS precisa de um grupo de segurança de rede para proteger as portas necessárias para o domínio gerido e bloquear todo o tráfego de entrada. Este grupo de segurança de rede funciona como uma camada extra de proteção para bloquear o acesso ao domínio gerido. Para visualizar as portas necessárias, consulte [os grupos de segurança da Rede e as portas necessárias.][network-ports]

    Se utilizar LDAP seguro, adicione uma regra ao grupo de segurança da rede para permitir a entrada de tráfego para a porta *TCP* *636*. Para mais informações, consulte [Lock Down Secure Acesso LDAP através da internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)

    Tome nota deste grupo de recursos-alvo, rede virtual de destino e sub-rede de rede virtual alvo. Estes nomes de recursos são usados durante o processo de migração.

1. Verifique a saúde do domínio gerido no portal Azure. Se tiver algum alerta para o domínio gerido, resolva-os antes de iniciar o processo de migração.
1. Opcionalmente, se planeia mover outros recursos para o modelo de implementação do Gestor de Recursos e rede virtual, confirme que esses recursos podem ser migrados. Para obter mais informações, consulte [a migração suportada pela Plataforma de recursos IaaS da Classic para a Resource Manager.][migrate-iaas]

    > [!NOTE]
    > Não converta a rede virtual Clássica numa rede virtual do Gestor de Recursos. Se o fizer, não há opção para retroceda ou restabelece o domínio gerido.

## <a name="prepare-the-managed-domain-for-migration"></a>Preparar o domínio gerido para a migração

A azure PowerShell é usada para preparar o domínio gerido para a migração. Estes passos incluem fazer uma cópia de segurança, fazer uma pausa na sincronização e eliminar o serviço de nuvem que acolhe O Azure AD DS. Quando este passo estiver concluído, o Azure AD DS é desligado por um período de tempo. Se o passo de preparação falhar, pode [voltar para o estado anterior.](#roll-back)

Para preparar o domínio gerido para a migração, complete os seguintes passos:

1. Instale o `Migrate-Aaads` guião na [PowerShell Gallery][powershell-script]. Este script de migração PowerShell é um livro digitalmente assinado pela equipa de engenharia Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Crie uma variável para manter as credenciais para o script de migração usando o [cmdlet Get-Credential.][get-credential]

    A conta de utilizador que especifica necessita de privilégios *globais de administrador* no seu inquilino Azure AD para ativar os privilégios Azure AD DS e, em seguida, *o Contribuinte* na sua subscrição Azure para criar os recursos Azure AD DS necessários.

    Quando solicitado, introduza uma conta de utilizador apropriada e senha:

    ```powershell
    $creds = Get-Credential
    ```
    
1. Defina uma variável para o seu ID de subscrição Azure. Se necessário, pode utilizar o [cmdlet Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) para listar e ver os seus IDs de subscrição. Forneça o seu próprio ID de subscrição no seguinte comando:

   ```powershell
   $subscriptionId = 'yourSubscriptionId'
   ```

1. Agora, executar o `Migrate-Aadds` cmdlet usando o parâmetro *-Preparar.* Forneça o *-ManagedDomainFqdn* para o seu próprio domínio gerido, como *aaddscontoso.com:*

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds `
        -SubscriptionId $subscriptionId
    ```

## <a name="migrate-the-managed-domain"></a>Migrar o domínio gerido

Com o domínio gerido preparado e apoiado, o domínio pode ser migrado. Este passo recria os VM do controlador de domínio Azure AD DS utilizando o modelo de implementação do Gestor de Recursos. Este passo pode levar 1 a 3 horas para ser concluído.

Executar o `Migrate-Aadds` cmdlet utilizando o parâmetro *-Cometer.* Forneça o *-ManagedDomainFqdn* para o seu próprio domínio gerido preparado na secção anterior, como *aaddscontoso.com:*

Especifique o grupo de recursos-alvo que contém a rede virtual para a qual pretende migrar a Azure AD DS, tal como *o myResourceGroup*. Forneça a rede virtual alvo, como *o myVnet,* e a sub-rede, como *o DomainServices*.

Depois de este comando funcionar, não pode então recuar:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

Depois de o script validar o domínio gerido está preparado para a migração, insira *Y* para iniciar o processo de migração.

> [!IMPORTANT]
> Não converta a rede virtual Clássica numa rede virtual do Gestor de Recursos durante o processo de migração. Se converter a rede virtual, não poderá então reverter ou restaurar o domínio gerido, uma vez que a rede virtual original já não existirá.

De dois em dois minutos durante o processo de migração, um indicador de progresso informa o estado atual, como mostra a produção do exemplo seguinte:

![Indicador de progresso da migração da Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

O processo de migração continua a decorrer, mesmo que feche o script PowerShell. No portal Azure, o estado do domínio gerido reporta como *Migração*.

Quando a migração estiver concluída com sucesso, pode ver o endereço IP do seu primeiro controlador de domínio no portal Azure ou através do Azure PowerShell. Uma estimativa de tempo sobre o segundo controlador de domínio disponível também é mostrada.

Nesta fase, pode opcionalmente mover outros recursos existentes do modelo de implementação Clássico e da rede virtual. Ou, você pode manter os recursos no modelo de implementação Clássico e espreitar as redes virtuais uns aos outros após a migração Azure AD DS está completa.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Teste e verifique a conectividade após a migração

Pode levar algum tempo para que o segundo controlador de domínio implemente e esteja disponível para ser utilizado no domínio gerido. O segundo controlador de domínio deve estar disponível 1-2 horas após o fim do cmdlet de migração. Com o modelo de implementação do Gestor de Recursos, os recursos de rede para o domínio gerido são apresentados no portal Azure ou no Azure PowerShell. Para verificar se o segundo controlador de domínio está disponível, consulte a página **Propriedades** para o domínio gerido no portal Azure. Se aparecerem dois endereços IP, o segundo controlador de domínio está pronto.

Depois de o segundo controlador de domínio estar disponível, complete os seguintes passos de configuração para a conectividade da rede com VMs:

* **Atualizar as definições do servidor DNS** Para permitir que outros recursos na rede virtual do Gestor de Recursos resolvam e utilizem o domínio gerido, atualize as definições de DNS com os endereços IP dos novos controladores de domínio. O portal Azure pode configurar automaticamente estas definições para si.

    Para saber mais sobre como configurar a rede virtual do Gestor de Recursos, consulte as [definições de DNS de atualização para a rede virtual Azure][update-dns].
* **Reiniciar VMs unidos pelo domínio (opcional)** À medida que os endereços IP do servidor DNS para os controladores de domínio AZure AD DS mudam, pode reiniciar quaisquer VMs unidos pelo domínio para que utilizem as novas definições do servidor DNS. Se as aplicações ou VMs tiverem configurações de DNS configuradas manualmente, actualifique-as manualmente com os novos endereços IP do servidor DNS dos controladores de domínio que são mostrados no portal Azure. Reiniciar VMs unidos ao domínio previne problemas de conectividade causados por endereços IP que não se refrescam.

Agora teste a ligação virtual da rede e a resolução de nomes. Num VM ligado à rede virtual do Gestor de Recursos, ou que lhe espreitam, experimente os seguintes testes de comunicação de rede:

1. Verifique se pode verificar o endereço IP de um dos controladores de domínio, tais como `ping 10.1.0.4`
    * Os endereços IP dos controladores de domínio são mostrados na página **Propriedades** para o domínio gerido no portal Azure.
1. Verificar a resolução de nomes do domínio gerido, tais como `nslookup aaddscontoso.com`
    * Especifique o nome DNS para o seu próprio domínio gerido para verificar se as definições de DNS estão corretas e resolve.

Para saber mais sobre outros recursos de rede, consulte [os recursos da Rede utilizados pela Azure AD DS][network-resources].

## <a name="optional-post-migration-configuration-steps"></a>Passos de configuração pós-migração opcionais

Quando o processo de migração estiver concluído com sucesso, algumas etapas de configuração opcionais incluem ativar registos de auditoria ou notificações de e-mail ou atualizar a política de senha de grão fino.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Subscreva registos de auditoria usando o Azure Monitor

A Azure AD DS expõe registos de auditoria para ajudar a resolver problemas e ver eventos nos controladores de domínio. Para obter mais informações, consulte [Ativar e utilizar registos de auditoria][security-audits].

Pode utilizar modelos para monitorizar informações importantes expostas nos registos. Por exemplo, o modelo de livro de registo de auditoria pode monitorizar possíveis bloqueios de conta no domínio gerido.

### <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para ser notificado quando um problema for detetado no domínio gerido, atualize as definições de notificação de e-mail no portal Azure. Para obter mais informações, consulte [as definições de notificação de configuração][notifications].

### <a name="update-fine-grained-password-policy"></a>Atualizar a política de senha de grão fino

Se necessário, pode atualizar a política de palavra-passe de grãos finos para ser menos restritiva do que a configuração padrão. Pode utilizar os registos de auditoria para determinar se uma definição menos restritiva faz sentido e, em seguida, configurar a política conforme necessário. Utilize as seguintes etapas de alto nível para rever e atualizar as definições de política para contas que são repetidamente bloqueadas após a migração:

1. [Configure a política de senhas][password-policy] para menos restrições no domínio gerido e observe os eventos nos registos de auditoria.
1. Se alguma conta de serviço estiver a utilizar senhas caducadas identificadas nos registos de auditoria, atualize essas contas com a senha correta.
1. Se um VM estiver exposto à internet, reveja nomes genéricos de conta como *administrador,* *utilizador* ou *hóspede* com altas tentativas de inscrição. Sempre que possível, atualize esses VMs para utilizar contas com nomes menos genericamente nomeados.
1. Utilize um vestígio de rede no VM para localizar a origem dos ataques e bloquear que esses endereços IP sejam capazes de tentar iniciar súpidos.
1. Quando houver problemas mínimos de bloqueio, atualize a política de senha de grão fino para ser tão restritiva quanto necessário.

## <a name="roll-back-and-restore-from-migration"></a>Recue e restaure da migração

Até um certo ponto do processo de migração, pode optar por retrocedar ou restaurar o domínio gerido.

### <a name="roll-back"></a>Recua

Se houver um erro quando executar o cmdlet PowerShell para se preparar para a migração no passo 2 ou para a migração em si no passo 3, o domínio gerido pode voltar para a configuração original. Este roll back requer a rede virtual original Classic. Os endereços IP ainda podem ser alterados após a reversão.

Executar o `Migrate-Aadds` cmdlet utilizando o parâmetro *-Abortar.* Forneça o *-ManagedDomainFqdn* para o seu próprio domínio gerido preparado numa secção anterior, como *aaddscontoso.com,* e o nome clássico da rede virtual, como *myClassicVnet*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

### <a name="restore"></a>Restauro

Como último recurso, os Serviços de Domínio AZure AD podem ser restaurados a partir da última cópia de segurança disponível. É dado um backup no passo 1 da migração para garantir que o backup mais atual está disponível. Esta cópia de segurança é armazenada durante 30 dias.

Para restaurar o domínio gerido a partir de cópias de segurança, [abra um bilhete de caixa de apoio utilizando o portal Azure][azure-support]. Forneça o seu diretório de identificação, nome de domínio e razão para restauro. O processo de suporte e restauro pode demorar vários dias a ser concluído.

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas após a migração para o modelo de implementação do Gestor de Recursos, reveja algumas das seguintes áreas comuns de resolução de problemas:

* [Problemas de união de domínios de resolução de problemas][troubleshoot-domain-join]
* [Problemas de bloqueio de conta de resolução de problemas][troubleshoot-account-lockout]
* [Problemas de inscrição na conta de resolução de problemas][troubleshoot-sign-in]
* [Resolução de problemas garante problemas de conectividade LDAP][tshoot-ldaps]

## <a name="next-steps"></a>Próximos passos

Com o seu domínio gerido migrado para o modelo de implementação do Gestor de Recursos, [crie e junte o domínio a um VM do Windows][join-windows] e, em seguida, [instale ferramentas de gestão][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/
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
[migrate-iaas]: ../virtual-machines/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential
[migration-benefits]: concepts-migration-benefits.md

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/