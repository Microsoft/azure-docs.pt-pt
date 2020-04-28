---
title: Migrate Azure AD Domain Services de uma rede virtual clássica [ Microsoft Docs
description: Saiba como migrar um serviço de domínio azure ad existente gerido do modelo de rede virtual Classic para uma rede virtual baseada em Recursos Manager.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 6acf9301367ae2c6947f6935c43f420d3d7cac65
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655022"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Migrate Azure AD Domain Services do modelo clássico de rede virtual para Gestor de Recursos

O Azure Ative Directory Domain Services (AD DS) suporta um movimento único para os clientes que utilizam atualmente o modelo de rede virtual Classic para o modelo de rede virtual Do Gestor de Recursos. Os domínios geridos pela Azure AD DS que utilizam o modelo de implementação do Gestor de Recursos fornecem funcionalidades adicionais, tais como a política de passwords de grãos finos, registos de auditoria e proteção de bloqueio de conta.

Este artigo descreve os benefícios e considerações para a migração, em seguida, os passos necessários para migrar com sucesso uma instância Azure AD DS existente.

> [!NOTE]
> Em 2017, a Azure AD Domain Services tornou-se disponível para hospedar numa rede azure Resource Manager. Desde então, conseguimos construir um serviço mais seguro utilizando as capacidades modernas do Gestor de Recursos Azure. Uma vez que as implementações do Azure Resource Manager substituem totalmente as implementações clássicas, as implementações clássicas da rede virtual Azure AD DS serão retiradas a 1 de março de 2023.
>
> Para mais informações, consulte o [aviso oficial de depreciação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>Visão geral do processo de migração

O processo de migração tem uma instância Azure AD DS existente que funciona numa rede virtual Clássica e a move para uma rede virtual existente do Gestor de Recursos. A migração é realizada usando powerShell, e tem duas fases principais de execução - *preparação* e *migração*.

![Visão geral do processo de migração para AD DS Azure](media/migrate-from-classic-vnet/migration-overview.png)

Na fase de *preparação,* o Azure AD DS requer uma cópia de segurança do domínio para obter a mais recente imagem instantânea de utilizadores, grupos e senhas sincronizadas para o domínio gerido. A sincronização é então desativada, e o serviço de nuvem que acolhe o domínio gerido pelo Azure AD DS é eliminado. Durante a fase de preparação, o domínio gerido pelo Azure AD DS não consegue autenticar os utilizadores.

![Fase de preparação para a migração da AD DS Azure](media/migrate-from-classic-vnet/migration-preparation.png)

Na fase de *migração,* os discos virtuais subjacentes aos controladores de domínio do domínio gerido clássico Azure AD DS são copiados para criar os VMs utilizando o modelo de implementação do Gestor de Recursos. O domínio gerido pelo Azure AD DS é então recriado, o que inclui a configuração LDAPS e DNS. A sincronização para a AD Azure é reiniciada e os certificados LDAP são restaurados. Não há necessidade de voltar a juntar nenhuma máquina a um domínio gerido pelo Azure AD DS – eles continuam a ser unidos ao domínio gerido e executados sem alterações.

![Migração de DS Azure](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Benefícios migratórios

Ao mover um domínio gerido pelo Azure AD DS utilizando este processo de migração, evita a necessidade de voltar a juntar as máquinas ao domínio gerido ou eliminar a instância Azure AD DS e criar uma de raiz. Os VMs continuam a ser associados ao domínio gerido pela AD DS Azure no final do processo de migração.

Após a migração, o Azure AD DS fornece muitas funcionalidades que só estão disponíveis para domínios usando redes virtuais do Gestor de Recursos, tais como:

* Suporte à política de senhas de grãos finos.
* Proteção de bloqueio de conta ad.
* Notificações por e-mail de alertas no domínio gerido pela AD DS azure.
* Registos de auditoria utilizando o Monitor Azure.
* Integração de Ficheiros Azure
* Integração de Insights HD

O Azure AD DS geriu domínios que utilizam uma rede virtual do Gestor de Recursos, ajudando-o a manter-se atualizado com as novas funcionalidades mais recentes. O suporte para O DS Azure utilizando redes virtuais clássicas deverá ser depreciado no futuro.

## <a name="example-scenarios-for-migration"></a>Exemplos de cenários para a migração

Alguns cenários comuns para a migração de um domínio gerido pela AD DS azure incluem os seguintes exemplos.

> [!NOTE]
> Não converta a rede virtual Clássica até confirmar uma migração bem sucedida. A conversão da rede virtual remove a opção de reverter ou restaurar o domínio gerido pelo Azure AD DS se houver algum problema durante as fases de migração e verificação.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrar AD DS do Migrate Azure para uma rede virtual existente do Gestor de Recursos (recomendado)

Um cenário comum é onde já transferiu outros recursos clássicos existentes para um modelo de implementação de Resource Manager e rede virtual. O peering é então usado da rede virtual Do Gestor de Recursos para a rede virtual Clássica que continua a executar O DS Azure. Esta abordagem permite que as aplicações e serviços do Gestor de Recursos utilizem a funcionalidade de autenticação e gestão do domínio gerido pelo Azure AD DS na rede virtual Clássica. Uma vez migrados, todos os recursos funcionam utilizando o modelo de implementação do Gestor de Recursos e a rede virtual.

![Migrate Azure AD DS para uma rede virtual existente do Gestor de Recursos](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Os passos de alto nível envolvidos neste cenário de migração incluem as seguintes partes:

1. Remova os gateways VPN existentes ou o peering de rede virtual configurado na rede virtual Clássica.
1. Migrar o domínio gerido da AD DS Azure utilizando os passos descritos neste artigo.
1. Teste e confirme uma migração bem sucedida, em seguida, apague a rede virtual Clássica.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migrar vários recursos, incluindo AD DS Azure

Neste cenário de exemplo, migra-se o Azure AD DS e outros recursos associados desde o modelo de implementação Classic até ao modelo de implementação do Gestor de Recursos. Se alguns recursos continuarem a funcionar na rede virtual Clássica ao lado do domínio gerido pelo Azure AD DS, todos podem beneficiar da migração para o modelo de implementação do Gestor de Recursos.

![Migrar vários recursos para o modelo de implantação do Gestor de Recursos](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Os passos de alto nível envolvidos neste cenário de migração incluem as seguintes partes:

1. Remova os gateways VPN existentes ou o peering de rede virtual configurado na rede virtual Clássica.
1. Migrar o domínio gerido da AD DS Azure utilizando os passos descritos neste artigo.
1. Configurar o peering de rede virtual entre a rede virtual Clássica e a rede Derecursos Manager.
1. Teste e confirme uma migração bem sucedida.
1. [Mova recursos clássicos adicionais como VMs][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrate Azure AD DS mas mantenha outros recursos na rede virtual clássica

Com este cenário de exemplo, você tem o mínimo de tempo de inatividade em uma sessão. Você apenas migra O AD DS Azure para uma rede virtual de Gestor de Recursos, e mantém os recursos existentes no modelo de implementação clássico e rede virtual. Num período de manutenção seguinte, pode migrar os recursos adicionais do modelo de implantação Classic e da rede virtual, conforme desejado.

![Migrar apenas Azure AD DS para o modelo de implementação do Gestor de Recursos](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Os passos de alto nível envolvidos neste cenário de migração incluem as seguintes partes:

1. Remova os gateways VPN existentes ou o peering de rede virtual configurado na rede virtual Clássica.
1. Migrar o domínio gerido da AD DS Azure utilizando os passos descritos neste artigo.
1. Configurar o peering virtual da rede entre a rede virtual Clássica e a nova rede virtual do Gestor de Recursos.
1. Mais tarde, [emigrar os recursos adicionais][migrate-iaas] da rede virtual clássica, conforme necessário.

## <a name="before-you-begin"></a>Antes de começar

À medida que se prepara e migra um domínio gerido pela Azure AD DS, existem algumas considerações em torno da disponibilidade de serviços de autenticação e gestão. O domínio gerido pela AD DS Azure não está disponível por um período de tempo durante a migração. Aplicações e serviços que dependem do Azure AD DS experimentam tempo de inatividade durante a migração.

> [!IMPORTANT]
> Leia todo este artigo e orientação de migração antes de iniciar o processo de migração. O processo de migração afeta a disponibilidade dos controladores de domínio Azure AD DS por períodos de tempo. Utilizadores, serviços e aplicações não podem autenticar contra o domínio gerido durante o processo de migração.

### <a name="ip-addresses"></a>Endereços IP

Os endereços IP do controlador de domínio para uma mudança de domínio gerida pelo Azure AD DS após a migração. Esta alteração inclui o endereço IP público para o ponto final lDAP seguro. Os novos endereços IP estão dentro da gama de endereços para a nova subnet na rede virtual do Gestor de Recursos.

No caso de retrocesso, os endereços IP podem mudar após a reversão.

O Azure AD DS normalmente utiliza os dois primeiros endereços IP disponíveis na gama de endereços, mas isso não está garantido. Não é possível especificar atualmente os endereços IP a utilizar após a migração.

### <a name="downtime"></a>Tempo de inatividade

O processo de migração envolve que os controladores de domínio estão offline por um período de tempo. Os controladores de domínio são inacessíveis enquanto o Azure AD DS é migrado para o modelo de implementação do Gestor de Recursos e rede virtual. Em média, o tempo de inatividade é de cerca de 1 a 3 horas. Este período de tempo é de quando os controladores de domínio são desligados até ao momento em que o primeiro controlador de domínio volta a funcionar. Esta média não inclui o tempo que o segundo controlador de domínio demora a replicar, ou o tempo que pode levar para migrar recursos adicionais para o modelo de implementação do Gestor de Recursos.

### <a name="account-lockout"></a>Bloqueio de conta

Os domínios geridos pela Azure AD DS que funcionam em redes virtuais clássicas não têm políticas de bloqueio de conta AD em vigor. Se os VMs forem expostos à internet, os atacantes podem usar métodos de pulverização de palavras-passe para forçar a sua entrada em contas. Não há nenhuma política de bloqueio de conta para parar essas tentativas. Para os domínios geridos pela Azure AD DS que utilizam o modelo de implementação do Gestor de Recursos e redes virtuais, as políticas de bloqueio de conta AD protegem contra estes ataques de spray de palavra-passe.

Por predefinição, 5 tentativas de senha em 2 minutos bloqueiam uma conta durante 30 minutos.

Uma conta bloqueada não pode ser usada para iniciar sessão, o que pode interferir com a capacidade de gerir o domínio gerido pelo Azure AD DS ou aplicações geridas pela conta. Depois de um domínio gerido pela AD DS azure ser migrado, as contas podem experimentar o que parece ser um bloqueio permanente devido a tentativas falhadas repetidas de iniciar sessão. Dois cenários comuns após a migração incluem:

* Uma conta de serviço que está a usar uma senha expirada.
    * A conta de serviço tenta insinserir-se repetidamente com uma senha caducada, que bloqueia a conta. Para corrigir isto, localize a aplicação ou VM com credenciais caducadas e atualize a palavra-passe.
* Uma entidade maliciosa está a usar tentativas de força bruta para entrar nas contas.
    * Quando os VMs são expostos à internet, os atacantes experimentam frequentemente combinações comuns de nomede utilizador e palavra-passe enquanto tentam assinar. Estas tentativas de inscrição repetidas podem bloquear as contas. Não é aconselhável utilizar contas de administrador com nomes genéricos como *administrador* ou *administrador,* por exemplo, para minimizar as contas administrativas de serem bloqueadas.
    * Minimize o número de VMs expostos à internet. Pode utilizar [o Azure Bastion][azure-bastion] para ligar de forma segura aos VMs utilizando o portal Azure.

Se suspeitar que algumas contas podem ser bloqueadas após a migração, os passos finais de migração descrevem como permitir a auditoria ou alterar as definições de política de passwords de grãos finos.

### <a name="roll-back-and-restore"></a>Recue e restaure

Se a migração não for bem sucedida, há processo para reverter ou restaurar um domínio gerido pelo Azure AD DS. O retrocesso é uma opção de autosserviço para devolver imediatamente o estado do domínio gerido antes da tentativa de migração. Os engenheiros de suporte Azure também podem restaurar um domínio gerido a partir de backup como último recurso. Para mais informações, veja [como recuar ou restaurar de uma migração falhada](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Restrições às redes virtuais disponíveis

Existem algumas restrições nas redes virtuais para as quais um domínio gerido pelo Azure AD DS pode ser migrado. A rede virtual destination Resource Manager deve satisfazer os seguintes requisitos:

* A rede virtual Do Gestor de Recursos deve estar na mesma subscrição do Azure que a rede virtual Clássica em que o Azure AD DS está atualmente implantado.
* A rede virtual do Gestor de Recursos deve estar na mesma região que a rede virtual Clássica em que o Azure AD DS está atualmente implantado.
* A subnet virtual do Gestor de Recursos deve ter pelo menos 3-5 endereços IP disponíveis.
* A subnet virtual da rede virtual do Gestor de Recursos deve ser uma subnet dedicada para o Azure AD DS, e não deve alojar outras cargas de trabalho.

Para obter mais informações sobre os requisitos da rede virtual, consulte considerações de design de rede virtual e opções de [configuração.][network-considerations]

## <a name="migration-steps"></a>Passos de migração

A migração para o modelo de implantação do Gestor de Recursos e rede virtual é dividida em 5 passos principais:

| Passo    | Realizado através  | Tempo estimado  | Tempo de inatividade  | Rolar para trás/Restaurar? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Passo 1 - Atualizar e localizar a nova rede virtual](#update-and-verify-virtual-network-settings) | Portal do Azure | 15 minutos | Não é necessário tempo de inatividade | N/D |
| [Passo 2 - Preparar o domínio gerido pela AD DS Azure para a migração](#prepare-the-managed-domain-for-migration) | PowerShell | 15 - 30 minutos, em média | O tempo de inatividade do Azure AD DS começa depois de este comando estar concluído. | Recue e restaure disponível. |
| [Passo 3 - Mover o domínio gerido do Azure AD DS para uma rede virtual existente](#migrate-the-managed-domain) | PowerShell | 1 - 3 horas, em média | Um controlador de domínio está disponível assim que este comando estiver concluído, termina o tempo de inatividade. | No caso do fracasso, tanto a reversão (self service) como a restauração estão disponíveis. |
| [Passo 4 - Teste e aguarde o controlador de domínio de réplica](#test-and-verify-connectivity-after-the-migration)| PowerShell e portal Azure | 1 hora ou mais, dependendo do número de testes | Ambos os controladores de domínio estão disponíveis e devem funcionar normalmente. | N/D. Uma vez que o primeiro VM é migrado com sucesso, não há opção para reversão ou restauro. |
| [Passo 5 - Passos de configuração opcionais](#optional-post-migration-configuration-steps) | Portal azure e VMs | N/D | Não é necessário tempo de inatividade | N/D |

> [!IMPORTANT]
> Para evitar tempo sem mais tempo, leia todo este artigo e orientação de migração antes de iniciar o processo de migração. O processo de migração afeta a disponibilidade dos controladores de domínio Azure AD DS durante um período de tempo. Utilizadores, serviços e aplicações não podem autenticar contra o domínio gerido durante o processo de migração.

## <a name="update-and-verify-virtual-network-settings"></a>Atualizar e verificar as definições de rede virtual

Antes de iniciar o processo de migração, complete as seguintes verificações e atualizações iniciais. Estes passos podem acontecer a qualquer momento antes da migração e não afetam o funcionamento do domínio gerido pela AD DS azure.

1. Atualize o ambiente Azure PowerShell local para a versão mais recente. Para completar os passos de migração, precisa de pelo menos a versão *2.3.2*.

    Para obter informações sobre como verificar e atualizar a sua versão PowerShell, consulte a [visão geral do Azure PowerShell][azure-powershell].

1. Criar ou escolher uma rede virtual existente do Gestor de Recursos.

    Certifique-se de que as definições de rede não bloqueiam as portas necessárias para o Azure AD DS. As portas devem estar abertas tanto na rede virtual Clássica como na rede virtual do Gestor de Recursos. Estas definições incluem tabelas de rotas (embora não seja recomendado usar tabelas de rotas) e grupos de segurança de rede.

    Para visualizar as portas necessárias, consulte os grupos de segurança da rede e as [portas necessárias][network-ports]. Para minimizar os problemas de comunicação da rede, recomenda-se esperar e aplicar um grupo de segurança de rede ou uma tabela de rotas à rede virtual do Gestor de Recursos após a migração concluída com sucesso.

    Tome nota deste grupo de recursos alvo, rede virtual alvo e subnet de rede virtual alvo. Estes nomes de recursos são usados durante o processo de migração.

1. Verifique a saúde de domínio gerida pelo Azure AD DS no portal Azure. Se tiver algum alerta para o domínio gerido, resolva-os antes de iniciar o processo de migração.
1. Opcionalmente, se planeia mover outros recursos para o modelo de implementação do Gestor de Recursos e rede virtual, confirme que esses recursos podem ser migrados. Para mais informações, consulte a [migração apoiada pela Plataforma de recursos IaaS de Classic para Resource Manager][migrate-iaas].

    > [!NOTE]
    > Não converta a rede virtual Clássica numa rede virtual do Gestor de Recursos. Se o fizer, não há opção de recuar ou restaurar o domínio gerido pelo Azure AD DS.

## <a name="prepare-the-managed-domain-for-migration"></a>Preparar o domínio gerido para a migração

O Azure PowerShell é utilizado para preparar o domínio gerido pelo Azure AD DS para a migração. Estes passos incluem tomar um backup, parar a sincronização, e apagar o serviço de nuvem que acolhe Azure AD DS. Quando este passo estiver concluído, o Azure AD DS é desligado por um período de tempo. Se o passo de preparação falhar, pode [voltar para o estado anterior.](#roll-back)

Para preparar o domínio gerido pela AD DS Azure para migração, complete os seguintes passos:

1. Instale `Migrate-Aaads` o script na [Galeria PowerShell.][powershell-script] Este roteiro de migração PowerShell é um assinado digitalmente pela equipa de engenharia da Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Crie uma variável para manter as credenciais pelo script de migração usando o cmdlet [Get-Credential.][get-credential]

    A conta de utilizador que especifica precisa de privilégios *de administrador global* no seu inquilino Azure AD para permitir que o Azure AD DS e, em seguida, os privilégios *contributivos* na sua subscrição Azure criem os recursos DS AD DS do Azure necessários.

    Quando solicitado, introduza uma conta de utilizador apropriada e uma palavra-passe:

    ```powershell
    $creds = Get-Credential
    ```

1. Agora, `Migrate-Aadds` corra o cmdlet utilizando o parâmetro *-Prepare.* Forneça o *-ManagedDomainFqdn* para o seu próprio domínio gerido pelo Azure AD DS, como *aaddscontoso.com:*

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Migrar o domínio gerido

Com o domínio gerido pela AD DS Azure preparado e apoiado, o domínio pode ser migrado. Este passo recria os VMs do controlador de domínio Azure AD Domain Services utilizando o modelo de implementação do Gestor de Recursos. Este passo pode levar 1 a 3 horas para ser concluído.

Executar `Migrate-Aadds` o cmdlet utilizando o *parâmetro -Cometer.* Forneça o *-ManagedDomainFqdn* para o seu próprio domínio gerido azure AD DS preparado na secção anterior, tais como *aaddscontoso.com:*

Especifique o grupo de recursos-alvo que contém a rede virtual para a qual pretende migrar o Azure AD DS, como o *myResourceGroup*. Fornecer a rede virtual alvo, como o *myVnet,* e a subnet, como *domainServices*.

Depois deste comando, não podes voltar atrás:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Depois do script validar o domínio gerido está preparado para a migração, insira *Y* para iniciar o processo de migração.

> [!IMPORTANT]
> Não converta a rede virtual Clássica numa rede virtual do Gestor de Recursos durante o processo de migração. Se converter a rede virtual, não pode então reverter ou restaurar o domínio gerido pelo Azure AD DS, uma vez que a rede virtual original já não existirá.

De dois em dois minutos durante o processo de migração, um indicador de progresso reporta o estado atual, como mostra a seguinte saída de exemplo:

![Indicador de progresso da migração da AD DS Azure](media/migrate-from-classic-vnet/powershell-migration-status.png)

O processo de migração continua a decorrer, mesmo que feche o script PowerShell. No portal Azure, o estado dos relatórios de domínio geridos como *Migração*.

Quando a migração estiver concluída com sucesso, pode ver o endereço IP do seu primeiro controlador de domínio no portal Azure ou através do Azure PowerShell. É também mostrada uma estimativa temporal do segundo controlador de domínio disponível.

Nesta fase, você pode opcionalmente mover outros recursos existentes do modelo de implementação Clássico e rede virtual. Ou, pode manter os recursos no modelo de implantação Classic e peer as redes virtuais uns aos outros após a migração Azure AD DS estar completa.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testar e verificar a conectividade após a migração

Pode levar algum tempo para que o segundo controlador de domínio implemente com sucesso e esteja disponível para uso no domínio gerido pelo Azure AD DS.

Com o modelo de implantação do Gestor de Recursos, os recursos de rede para o domínio gerido pelo Azure AD DS são mostrados no portal Azure ou na Azure PowerShell. Para saber mais sobre o que são e fazem estes recursos de rede, consulte [os recursos da Rede utilizados pela Azure AD DS][network-resources].

Quando estiver disponível pelo menos um controlador de domínio, complete os seguintes passos de configuração para a conectividade da rede com VMs:

* **Atualizar as definições do servidor DNS** Para permitir que outros recursos na rede virtual do Gestor de Recursos resolvam e utilizem o domínio gerido pelo Azure AD DS, atualize as definições de DNS com os endereços IP dos novos controladores de domínio. O portal Azure pode configurar automaticamente estas definições para si. Para saber mais sobre como configurar a rede virtual do Gestor de Recursos, consulte [as definições de DNS de Atualização para a rede virtual Azure][update-dns].
* **Reiniciar VMs unidos pelo domínio** - À medida que os endereços IP do servidor DNS para os controladores de domínio Azure AD DS mudam, reinicie quaisquer VMs associados ao domínio para que utilizem as novas definições do servidor DNS. Se as aplicações ou VMs tiverem configurado manualmente as definições de DNS, atualize-as manualmente com os novos endereços IP do servidor DNS dos controladores de domínio que são mostrados no portal Azure.

Agora teste a ligação virtual da rede e a resolução de nomes. Num VM que esteja ligado à rede virtual do Gestor de Recursos, ou que lhe tenha sido observada, experimente os seguintes testes de comunicação de rede:

1. Verifique se pode verificar o endereço IP de um dos controladores de domínio, como`ping 10.1.0.4`
    * Os endereços IP dos controladores de domínio são mostrados na página **Propriedades** para o domínio gerido azure AD DS no portal Azure.
1. Verificar a resolução de nomes do domínio gerido, tais como`nslookup aaddscontoso.com`
    * Especifique o nome DNS para o seu próprio domínio gerido pelo Azure AD DS para verificar se as definições de DNS estão corretas e resolvem.

O segundo controlador de domínio deve estar disponível 1-2 horas após o acabamento do cmdlet migratório. Para verificar se o segundo controlador de domínio está disponível, consulte a página **Propriedades** do domínio gerido pelo Azure AD DS no portal Azure. Se dois endereços IP mostrados, o segundo controlador de domínio está pronto.

## <a name="optional-post-migration-configuration-steps"></a>Passos opcionais de configuração pós-migração

Quando o processo de migração estiver concluído com sucesso, algumas etapas de configuração opcionais incluem permitir registos de auditoria ou notificações de e-mail, ou atualizar a política de senha de grãos finos.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Subscreva os registos de auditoria utilizando o Monitor Azure

O Azure AD DS expõe registos de auditoria para ajudar a resolver problemas e a visualizar eventos nos controladores de domínio. Para mais informações, consulte [Enable e utilize registos de auditoria][security-audits].

Pode utilizar modelos para monitorizar informações importantes expostas nos registos. Por exemplo, o modelo de livro de registo de auditoria pode monitorizar possíveis bloqueios de conta no domínio gerido pelo Azure AD DS.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>Configure Azure AD Domain Services notificações de e-mail

Para ser notificado quando um problema é detetado no domínio gerido pelo Azure AD DS, atualize as definições de notificação de e-mail no portal Azure. Para mais informações, consulte as definições de [notificação do Configure][notifications].

### <a name="update-fine-grained-password-policy"></a>Atualizar a política de senha de grãos finos

Se necessário, pode atualizar a política de palavra-passe de grãos finos para ser menos restritiva do que a configuração padrão. Pode utilizar os registos de auditoria para determinar se uma definição menos restritiva faz sentido e, em seguida, configurar a apólice conforme necessário. Utilize as seguintes medidas de alto nível para rever e atualizar as definições de políticas para contas que são repetidamente bloqueadas após a migração:

1. [Configure a política de palavra-passe][password-policy] para menos restrições no domínio gerido pelo Azure AD DS e observe os eventos nos registos de auditoria.
1. Se alguma conta de serviço estiver a utilizar senhas caducadas identificadas nos registos de auditoria, atualize essas contas com a palavra-passe correta.
1. Se um VM for exposto à internet, reveja os nomes genéricos de contas como *administrador,* *utilizador*ou *hóspede* com tentativas de insessão elevadas. Sempre que possível, atualize esses VMs para utilizar contas menos genericamente nomeadas.
1. Utilize um vestígio de rede no VM para localizar a origem dos ataques e bloquear que os endereços IP sejam capazes de tentar iniciar sessão.
1. Quando houver problemas mínimos de bloqueio, atualize a política de senhas de grãos finos para ser tão restritiva quanto necessário.

### <a name="creating-a-network-security-group"></a>Criar um grupo de segurança de rede

O Azure AD DS precisa de um grupo de segurança de rede para proteger os portos necessários para o domínio gerido e bloquear todo o tráfego de entrada. Este grupo de segurança da rede funciona como uma camada extra de proteção para bloquear o acesso ao domínio gerido, e não é criado automaticamente. Para criar o grupo de segurança da rede e abrir as portas necessárias, reveja os seguintes passos:

1. No portal Azure, selecione o seu recurso Azure AD DS. Na página geral, é apresentado um botão para criar um grupo de segurança de rede se não houver nenhum associado aos Serviços de Domínio Azure AD.
1. Se utilizar lDAP seguro, adicione uma regra ao grupo de segurança da rede para permitir o tráfego de entrada para a porta *TCP* *636*. Para mais informações, consulte [Configure secure LDAP][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Recue e restaure da migração

Até um certo ponto no processo de migração, pode optar por reverter ou restaurar o domínio gerido pelo Azure AD DS.

### <a name="roll-back"></a>Rolar para trás

Se houver um erro quando executa o cmdlet PowerShell para se preparar para a migração no passo 2 ou para a migração em si no passo 3, o domínio gerido pelo Azure AD DS pode voltar à configuração original. Este retrocesso requer a rede virtual clássica original. Note que os endereços IP podem ainda mudar após a reversão.

Executar `Migrate-Aadds` o cmdlet utilizando o parâmetro *-Abortar.* Forneça o *-ManagedDomainFqdn* para o seu próprio domínio gerido azure AD DS preparado numa secção anterior, como *aaddscontoso.com,* e o nome clássico da rede virtual, como o *myClassicVnet:*

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Restauro

Como último recurso, os Serviços de Domínio Azure AD podem ser restaurados a partir do último backup disponível. É dado um backup no passo 1 da migração para garantir que o backup mais atual está disponível. Esta reserva está armazenada por 30 dias.

Para restaurar o domínio gerido pela AD DS Azure a partir de backup, [abra um bilhete de caixa de suporte utilizando o portal Azure][azure-support]. Forneça o seu ID de diretório, nome de domínio e razão para restaurar. O processo de suporte e restauro pode demorar vários dias a ser concluído.

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas após a migração para o modelo de implementação do Gestor de Recursos, reveja algumas das seguintes áreas comuns de resolução de problemas:

* [Problemas de união de domínios de resolução de problemas][troubleshoot-domain-join]
* [Problemas de bloqueio da conta de resolução de problemas][troubleshoot-account-lockout]
* [Problemas de sessão de problemas de inscrição na conta][troubleshoot-sign-in]
* [Problemas seguros problemas de conectividade LDAP seguros][tshoot-ldaps]

## <a name="next-steps"></a>Passos seguintes

Com o seu domínio gerido pelo Azure AD DS migrado para o modelo de implementação do Gestor de Recursos, [crie e junte um VM do Windows][join-windows] e, em seguida, [instale ferramentas][tutorial-create-management-vm]de gestão .

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
