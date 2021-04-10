---
title: Alertas e resoluções comuns nos serviços de domínio Azure AD | Microsoft Docs
description: Saiba como resolver os alertas comuns gerados como parte do estado de saúde dos Serviços de Domínio do Diretório Ativo Azure
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 4caf804a274956556d6e9ca396c8f08594b11a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092871"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Questões conhecidas: Alertas e resoluções comuns nos Serviços de Domínio do Diretório Ativo do Azure

Como parte central da identidade e autenticação para aplicações, o Azure Ative Directory Domain Services (Azure AD DS) às vezes tem problemas. Se tiver problemas, há alguns alertas comuns e passos associados de resolução de problemas para ajudá-lo a pôr as coisas a funcionar novamente. A qualquer momento, também pode abrir um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

Este artigo fornece informações sobre resolução de problemas para alertas comuns em Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Diretório desaparecido

### <a name="alert-message"></a>Mensagem de alerta

*O diretório AD Azure associado ao seu domínio gerido pode ter sido eliminado. O domínio gerido já não se encontra numa configuração suportada. A Microsoft não consegue monitorizar, gerir, corrigir e sincronizar o seu domínio gerido.*

### <a name="resolution"></a>Resolução

Este erro é geralmente causado quando uma subscrição do Azure é transferida para um novo diretório AD Azure e o antigo diretório AD Ad Azure que está associado ao Azure AD DS é eliminado.

Este erro é irrecuperável. Para resolver o alerta, [elimine o seu domínio gerido e](delete-aadds.md) recrie-o no seu novo diretório. Se tiver problemas em eliminar o domínio gerido, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C está a correr neste diretório

### <a name="alert-message"></a>Mensagem de alerta

*Os Serviços de Domínio Azure AD não podem ser ativados num Diretório AD B2C Azure.*

### <a name="resolution"></a>Resolução

A Azure AD DS sincroniza-se automaticamente com um diretório AD Azure. Se o diretório AD Azure estiver configurado para B2C, o Azure AD DS não pode ser implantado e sincronizado.

Para utilizar o Azure AD DS, tem de recriar o seu domínio gerido num diretório AD B2C não-Ad B2C utilizando os seguintes passos:

1. [Elimine o domínio gerido](delete-aadds.md) do seu diretório AD Azure existente.
1. Crie um novo diretório AD Azure que não seja um diretório Azure AD B2C.
1. [Criar um domínio gerido por substituição](tutorial-create-instance.md).

A saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: O endereço está numa gama pública de IP

### <a name="alert-message"></a>Mensagem de alerta

*A gama de endereços IP para a rede virtual na qual ativou os Serviços de Domínio Azure AD está numa gama pública de IP. Os Serviços de Domínio Azure AD devem ser ativados numa rede virtual com uma gama de endereços IP privada. Esta configuração tem impacto na capacidade da Microsoft de monitorizar, gerir, corrigir e sincronizar o seu domínio gerido.*

### <a name="resolution"></a>Resolução

Antes de começar, certifique-se de que compreende [os espaços de endereços IP v4 privados](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Dentro de uma rede virtual, os VMs podem fazer pedidos aos recursos Azure na mesma gama de endereços IP configuradas para a sub-rede. Se configurar um intervalo de endereços IP público para uma sub-rede, os pedidos encaminhados para dentro de uma rede virtual podem não atingir os recursos web pretendidos. Esta configuração pode levar a erros imprevisíveis com Azure AD DS.

> [!NOTE]
> Se possuir a gama de endereços IP na internet configurada na sua rede virtual, este alerta pode ser ignorado. No entanto, os Serviços de Domínio AD AZure não podem comprometer-se com o [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) com esta configuração, uma vez que pode levar a erros imprevisíveis.

Para resolver este alerta, elimine o seu domínio gerido existente e recrie-o numa rede virtual com uma gama de endereços IP privada. Este processo é disruptivo, uma vez que o domínio gerido não está disponível e quaisquer recursos personalizados que tenha criado como OUs ou contas de serviço perdem-se.

1. [Elimine o domínio gerido](delete-aadds.md) do seu diretório.
1. Para atualizar o intervalo de endereços IP de rede virtual, procure e selecione *a rede Virtual* no portal Azure. Selecione a rede virtual para Azure AD DS que tenha incorretamente um conjunto de intervalo de endereços IP público.
1. Em **Definições**, selecione *Espaço de Endereço*.
1. Atualize o intervalo de endereços escolhendo o intervalo de endereços existente e editando-o, ou adicionando um intervalo de endereços adicional. Certifique-se de que a nova gama de endereços IP está num intervalo de IP privado. Quando estiver pronto, **guarde** as alterações.
1. Selecione **sub-redes** na navegação à esquerda.
1. Escolha a sub-rede que pretende editar ou crie uma sub-rede adicional.
1. Atualizar ou especificar um intervalo de endereço IP privado, em **seguida, guardar** as suas alterações.
1. [Criar um domínio gerido por substituição](tutorial-create-instance.md). Certifique-se de que escolhe a sub-rede de rede virtual atualizada com um intervalo de endereços IP privado.

A saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: A sua subscrição Azure não é encontrada

### <a name="alert-message"></a>Mensagem de alerta

*A sua subscrição Azure associada ao seu domínio gerido foi eliminada.  Os Serviços de Domínio Azure AD requerem uma subscrição ativa para continuar a funcionar corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma subscrição ativa e não pode ser movido para uma subscrição diferente. Se a subscrição Azure a que o domínio gerido foi associado for eliminada, deve recriar uma subscrição do Azure e domínio gerido.

1. [Criar uma subscrição Azure](../cost-management-billing/manage/create-subscription.md).
1. [Elimine o domínio gerido](delete-aadds.md) do seu diretório AD Azure existente.
1. [Criar um domínio gerido por substituição](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: A sua subscrição do Azure está desativada

### <a name="alert-message"></a>Mensagem de alerta

*A subscrição do Azure associada ao seu domínio gerido não está ativa.  Os Serviços de Domínio Azure AD requerem uma subscrição ativa para continuar a funcionar corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma subscrição ativa. Se a subscrição do Azure a que o domínio gerido estava associado não estiver ativa, deve renová-la para reativar a subscrição.

1. [Renovar a sua assinatura Azure](../cost-management-billing/manage/subscription-disabled.md).
2. Uma vez renovada a subscrição, uma notificação Azure AD DS permite-lhe voltar a ativar o domínio gerido.

Quando o domínio gerido é novamente ativado, a saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Diretórios de assinaturas

### <a name="alert-message"></a>Mensagem de alerta

*A subscrição utilizada pela Azure AD Domain Services foi transferida para outro diretório. Os Serviços de Domínio Azure AD precisam de ter uma subscrição ativa no mesmo diretório para funcionar corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma subscrição ativa e não pode ser movido para uma subscrição diferente. Se a subscrição do Azure a que o domínio gerido foi associado for movida, mova a subscrição de volta para o diretório anterior, ou [elimine](delete-aadds.md) o seu domínio gerido do diretório existente e [crie um domínio gerido de substituição na subscrição escolhida](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Os recursos para o seu domínio gerido não podem ser encontrados

### <a name="alert-message"></a>Mensagem de alerta

*Um recurso utilizado para o seu domínio gerido foi eliminado. Este recurso é necessário para que os Serviços de Domínio AZure AD funcionem corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS cria recursos adicionais para funcionar corretamente, tais como endereços IP públicos, interfaces de rede virtuais e um equilibrador de carga. Se algum destes recursos for eliminado, o domínio gerido encontra-se num estado não suportado e impede que o domínio seja gerido. Para obter mais informações sobre estes recursos, consulte [os recursos da Rede utilizados pela Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Este alerta é gerado quando um destes recursos necessários é eliminado. Se o recurso foi eliminado há menos de 4 horas, existe a possibilidade de a plataforma Azure poder recriar automaticamente o recurso eliminado. Os seguintes passos descrevem como verificar o estado de saúde e a data de verificação da eliminação dos recursos:

1. No portal Azure, procure e selecione **Serviços de Domínio**. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. Na navegação à esquerda, selecione **Health**.
1. Na página de saúde, selecione o alerta com o ID *AADDS109*.
1. O alerta tem um tempo para quando foi encontrado pela primeira vez. Se essa estada de tempo for inferior a 4 horas atrás, a plataforma Azure poderá ser capaz de recriar automaticamente o recurso e resolver o alerta por si só.

    Se o alerta tiver mais de 4 horas, o domínio gerido encontra-se num estado irrecuperável. [Eliminar o domínio gerido](delete-aadds.md) e, em seguida, [criar um domínio gerido por substituição](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: A sub-rede associada ao seu domínio gerido está cheia

### <a name="alert-message"></a>Mensagem de alerta

*A sub-rede selecionada para a implantação dos Serviços de Domínio Ad AD Azure está cheia e não tem espaço para o controlador de domínio adicional que precisa de ser criado.*

### <a name="resolution"></a>Resolução

A sub-rede de rede virtual para Azure AD DS necessita de endereços IP suficientes para os recursos automaticamente criados. Este espaço de endereço IP inclui a necessidade de criar recursos de substituição se houver um evento de manutenção. Para minimizar o risco de ficar sem endereços IP disponíveis, não implemente recursos adicionais, como os seus próprios VMs, na mesma sub-rede de rede virtual que o domínio gerido.

Este erro é irrecuperável. Para resolver o alerta, [elimine o seu domínio gerido e](delete-aadds.md) recrie-o. Se tiver problemas em eliminar o domínio gerido, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Diretor de serviço não autorizado

### <a name="alert-message"></a>Mensagem de alerta

*Um responsável de serviço que a Azure AD Domain Services utiliza para servir o seu domínio não está autorizado a gerir recursos na subscrição do Azure. O diretor de serviço precisa de obter permissões para servir o seu domínio gerido.*

### <a name="resolution"></a>Resolução

Alguns principais de serviço gerados automaticamente são usados para gerir e criar recursos para um domínio gerido. Se as permissões de acesso a um destes principais de serviço forem alteradas, o domínio não consegue gerir corretamente os recursos. Os seguintes passos mostram-lhe como compreender e, em seguida, conceder permissões de acesso a um diretor de serviço:

1. Leia sobre [o controlo de acesso baseado em funções Azure e como conceder acesso a aplicações no portal Azure](../role-based-access-control/role-assignments-portal.md).
2. Reveja o acesso que o diretor do serviço com o ID *abba844e-bc0e-44b0-947a-dc74e5d09022* tem e conceda o acesso que foi negado em data anterior.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Não há endereço IP suficiente no domínio gerido

### <a name="alert-message"></a>Mensagem de alerta

*Identificamos que a sub-rede da rede virtual neste domínio pode não ter endereços IP suficientes. Os Serviços de Domínio Azure AD precisam de pelo menos dois endereços IP disponíveis dentro da sub-rede em que está ativado. Recomendamos ter pelo menos 3-5 endereços IP sobressalentes dentro da sub-rede. Isto pode ter ocorrido se outras máquinas virtuais forem implantadas dentro da sub-rede, esgotando assim o número de endereços IP disponíveis ou se houver uma restrição no número de endereços IP disponíveis na sub-rede.*

### <a name="resolution"></a>Resolução

A sub-rede de rede virtual para Azure AD DS necessita de endereços IP suficientes para os recursos criados automaticamente. Este espaço de endereço IP inclui a necessidade de criar recursos de substituição se houver um evento de manutenção. Para minimizar o risco de ficar sem endereços IP disponíveis, não implemente recursos adicionais, como os seus próprios VMs, na mesma sub-rede de rede virtual que o domínio gerido.

Para resolver este alerta, elimine o seu domínio gerido existente e reu crie-o numa rede virtual com uma gama de endereços IP suficientemente grande. Este processo é disruptivo, uma vez que o domínio gerido não está disponível e quaisquer recursos personalizados que tenha criado como OUs ou contas de serviço perdem-se.

1. [Elimine o domínio gerido](delete-aadds.md) do seu diretório.
1. Para atualizar o intervalo de endereços IP de rede virtual, procure e selecione *a rede Virtual* no portal Azure. Selecione a rede virtual para o domínio gerido que tem a pequena gama de endereços IP.
1. Em **Definições**, selecione *Espaço de Endereço*.
1. Atualize o intervalo de endereços escolhendo o intervalo de endereços existente e editando-o, ou adicionando um intervalo de endereços adicional. Certifique-se de que a nova gama de endereços IP é suficientemente grande para a gama de sub-redes do domínio gerido. Quando estiver pronto, **guarde** as alterações.
1. Selecione **sub-redes** na navegação à esquerda.
1. Escolha a sub-rede que pretende editar ou crie uma sub-rede adicional.
1. Atualize ou especifique um intervalo de endereço IP suficientemente grande e, em **seguida, guarde** as suas alterações.
1. [Criar um domínio gerido por substituição](tutorial-create-instance.md). Certifique-se de que escolhe a sub-rede de rede virtual atualizada com um alcance de endereço IP suficientemente grande.

A saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Os recursos são irrecuperáveis

### <a name="alert-message"></a>Mensagem de alerta

*Os recursos utilizados pelos Serviços de Domínio AD Azure foram detetados num estado inesperado e não podem ser recuperados.*

### <a name="resolution"></a>Resolução

Este erro é irrecuperável. Para resolver o alerta, [elimine o seu domínio gerido e](delete-aadds.md) recrie-o. Se tiver problemas em eliminar o domínio gerido, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Sub-rede inválida

### <a name="alert-message"></a>Mensagem de alerta

*A sub-rede selecionada para a implantação dos Serviços de Domínio Azure AD é inválida e não pode ser utilizada.*

### <a name="resolution"></a>Resolução

Este erro é irrecuperável. Para resolver o alerta, [elimine o seu domínio gerido e](delete-aadds.md) recrie-o. Se tiver problemas em eliminar o domínio gerido, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Os recursos estão bloqueados

### <a name="alert-message"></a>Mensagem de alerta

*Um ou mais dos recursos de rede utilizados pelo domínio gerido não podem ser operados, uma vez que o âmbito do objetivo foi bloqueado.*

### <a name="resolution"></a>Resolução

Os bloqueios de recursos podem ser aplicados aos recursos da Azure para evitar a mudança ou eliminação. Como o Azure AD DS é um serviço gerido, a plataforma Azure necessita da capacidade de fazer alterações de configuração. Se for aplicado um bloqueio de recursos em alguns dos componentes Azure AD DS, a plataforma Azure não pode executar as suas tarefas de gestão.

Para verificar se há bloqueios de recursos nos componentes AZURE AD DS e removê-los, complete os seguintes passos:

1. Para cada um dos componentes de rede do domínio gerido no seu grupo de recursos, como rede virtual, interface de rede ou endereço IP público, verifique os registos de funcionamento no portal Azure. Estes registos de operação devem indicar por que uma operação está a falhar e onde é aplicada uma fechadura de recursos.
1. Selecione o recurso onde é aplicada uma fechadura e, em seguida, em **Fechaduras,** selecione e retire o(s) de bloqueio).

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Os recursos são inutilizáveis

### <a name="alert-message"></a>Mensagem de alerta

*Um ou mais dos recursos de rede utilizados pelo domínio gerido não podem ser operados devido à restrição de política.*

### <a name="resolution"></a>Resolução

As políticas são aplicadas aos recursos da Azure e aos grupos de recursos que controlam as ações de configuração permitidas. Como o Azure AD DS é um serviço gerido, a plataforma Azure necessita da capacidade de fazer alterações de configuração. Se for aplicada uma política em alguns dos componentes Azure AD DS, a plataforma Azure pode não ser capaz de executar as suas tarefas de gestão.

Para verificar as políticas aplicadas nos componentes AZURE AD DS e atualizá-los, complete os seguintes passos:

1. Para cada um dos componentes de rede do domínio gerido no seu grupo de recursos, como rede virtual, NIC ou endereço IP público, verifique os registos de operação no portal Azure. Estes registos de operação devem indicar por que razão uma operação está a falhar e onde é aplicada uma política restritiva.
1. Selecione o recurso onde uma política é aplicada, em seguida, em **Políticas,** selecione e edite a política para que seja menos restritiva.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: A sincronização não está concluída há algum tempo

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerido foi sincronizado pela última vez com Azure AD em [data]. Os utilizadores podem não conseguir entrar no domínio gerido ou os membros do grupo podem não estar em sintonia com o Azure AD.*

### <a name="resolution"></a>Resolução

[Verifique se a saúde do Azure AD DS](check-health.md) é necessário obter quaisquer alertas que indiquem problemas na configuração do domínio gerido. Os problemas com a configuração da rede podem bloquear a sincronização a partir do Azure AD. Se conseguir resolver alertas que indiquem um problema de configuração, aguarde duas horas e volte para ver se a sincronização foi concluída com sucesso.

As seguintes razões comuns fazem com que a sincronização pare num domínio gerido:

* A conectividade de rede necessária está bloqueada. Para saber mais sobre como verificar a rede virtual Azure para encontrar problemas e o que é necessário, consulte os grupos de segurança da rede de [resolução de problemas](alert-nsg.md) e os requisitos de [rede para Azure AD DS](network-considerations.md).
*  A sincronização de palavras-passe não foi configurada ou concluída com sucesso quando o domínio gerido foi implementado. Pode configurar a sincronização [de palavras-passe para utilizadores apenas na nuvem](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) ou [utilizadores híbridos a partir de pré-pré-m.](tutorial-configure-password-hash-sync.md)

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Uma cópia de segurança não é tomada há algum tempo

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerido foi o último a ser apoiado [data].*

### <a name="resolution"></a>Resolução

[Verifique se a saúde do Azure AD DS](check-health.md) é responsável por alertas que indiquem problemas na configuração do domínio gerido. Os problemas com a configuração da rede podem impedir a plataforma Azure de fazer cópias de segurança com sucesso. Se conseguir resolver alertas que indiquem um problema de configuração, aguarde duas horas e volte para ver se a sincronização foi concluída com sucesso.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Suspensão devido a subscrição desativada

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerido é suspenso porque a subscrição Azure associada ao domínio não está ativa.*

### <a name="resolution"></a>Resolução

> [!WARNING]
> Se um domínio gerido for suspenso por um longo período de tempo, existe o perigo de ser eliminado. Resolva o motivo da suspensão o mais rápido possível. Para mais informações, consulte [Compreender os estados suspensos da Azure AD DS](suspension.md).

O Azure AD DS requer uma subscrição ativa. Se a subscrição do Azure a que o domínio gerido estava associado não estiver ativa, deve renová-la para reativar a subscrição.

1. [Renovar a sua assinatura Azure](../cost-management-billing/manage/subscription-disabled.md).
2. Uma vez renovada a subscrição, uma notificação Azure AD DS permite-lhe voltar a ativar o domínio gerido.

Quando o domínio gerido é novamente ativado, a saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Suspensão devido a uma configuração inválida

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerido é suspenso devido a uma configuração inválida. O serviço não foi capaz de gerir, corrigir ou atualizar os controladores de domínio para o seu domínio gerido durante muito tempo.*

### <a name="resolution"></a>Resolução

> [!WARNING]
> Se um domínio gerido for suspenso por um longo período de tempo, existe o perigo de ser eliminado. Resolva o motivo da suspensão o mais rápido possível. Para mais informações, consulte [Compreender os estados suspensos da Azure AD DS](suspension.md).

[Verifique se a saúde do Azure AD DS](check-health.md) é responsável por alertas que indiquem problemas na configuração do domínio gerido. Se conseguir resolver alertas que indiquem um problema de configuração, aguarde duas horas e volte para ver se a sincronização está concluída. Quando estiver pronto, abra um pedido de [suporte Azure][azure-support] para ree capacitar o domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md