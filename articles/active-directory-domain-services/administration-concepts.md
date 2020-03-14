---
title: Conceitos de gestão para serviços de domínio azure AD  Microsoft Docs
description: Saiba como administrar um domínio gerido pelo Azure Ative Directory Domain Services e o comportamento das contas de utilizador e palavras-passe
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 682935fa2324b8de4992ab2f90c7f71e05c4f8ac
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264235"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Conceitos de gestão para contas de utilizador, senhas e administração em Serviços de Domínio de Diretório Ativo Azure

Quando cria e gere um domínio gerido pelo Azure Ative Directory Domain Services (AD DS), existem algumas diferenças de comportamento em comparação com um ambiente AD DS tradicional no local. Usa as mesmas ferramentas administrativas em DS Azure como um domínio autogerido, mas não pode aceder diretamente aos controladores de domínio (DC). Há também algumas diferenças no comportamento das políticas de password e de hashes de senha dependendo da origem da criação da conta de utilizador.

Este artigo conceptual detalha como administrar um domínio gerido pelo Azure AD DS e o comportamento diferente das contas dos utilizadores dependendo da forma como são criados.

## <a name="domain-management"></a>Gestão de domínios

No Azure AD DS, os controladores de domínio (DCs) que contêm todos os recursos, como utilizadores e grupos, credenciais e políticas fazem parte do serviço gerido. Para a redundância, dois DCs são criados como parte de um domínio gerido pela AD DS azure. Não pode sintetar estes DCs para executar tarefas de gestão. Em vez disso, cria um VM de gestão que se juntou ao domínio gerido pelo Azure AD DS e, em seguida, instala as suas ferramentas regulares de gestão de DS DS. Pode utilizar o Centro Administrativo de Diretório Ativo ou a Consola de Gestão da Microsoft (MMC) snap-ins como objetos DNS ou Group Policy, por exemplo.

## <a name="user-account-creation"></a>Criação de conta de utilizador

As contas de utilizador podem ser criadas em DS AD S Azure de várias maneiras. A maioria das contas de utilizador são sincronizadas a partir de Azure AD, que também pode incluir a conta de utilizador sincronizada a partir de um ambiente AD DS no local. Também pode criar contas manualmente em DS AD Azure. Algumas funcionalidades, como a sincronização inicial da palavra-passe ou a política de passwords, comportam-se de forma diferente dependendo de como e onde as contas dos utilizadores são criadas.

* A conta de utilizador pode ser sincronizada a partir de Azure AD. Isto inclui contas de utilizadores exclusivamente em nuvem criadas diretamente em Azure AD, e contas híbridas de utilizador sincronizadas a partir de um ambiente AD DS no local usando Azure AD Connect.
    * A maioria das contas de utilizadores em Azure AD DS são criadas através do processo de sincronização a partir de Azure AD.
* A conta de utilizador pode ser criada manualmente num domínio gerido por Azure AD DS, e não existe em Azure AD.
    * Se necessitar de criar contas de serviço para aplicações que só funcionam em DS AD Azure, pode criá-las manualmente no domínio gerido. Como a sincronização é uma forma de Azure AD, as contas de utilizador criadas em Azure AD DS não são sincronizadas de volta ao Azure AD.

## <a name="password-policy"></a>Política de senha

O Azure AD DS inclui uma política de senha padrão que define definições para coisas como bloqueio de conta, idade máxima de senha e complexidade de senha. Definições como a política de bloqueio de conta aplicam-se a todos os utilizadores em Azure AD DS, independentemente da forma como o utilizador foi criado como delineado na secção anterior. Algumas definições, como o comprimento mínimo da palavra-passe e a complexidade da palavra-passe, aplicam-se apenas aos utilizadores criados diretamente no Azure ADDS.

Pode criar as suas próprias políticas de senha personalizadas para anular a política de padrão em Azure AD DS. Estas políticas personalizadas podem então ser aplicadas a grupos específicos de utilizadores, se necessário.

Para obter mais informações sobre as diferenças de como as políticas de password são aplicadas dependendo da origem da criação do utilizador, consulte as políticas de password e bloqueio de [conta em domínios geridos][password-policy].

## <a name="password-hashes"></a>Hashes de senha

Para autenticar os utilizadores no domínio gerido, o Azure AD DS necessita de hashes de senha num formato adequado para nt LAN Manager (NTLM) e autenticação Kerberos. A Azure AD não gera nem armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até ativar o Azure AD DS para o seu inquilino. Por razões de segurança, a Azure AD também não armazena credenciais de senha em formato de texto claro. Portanto, a Azure AD não pode gerar automaticamente estas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos utilizadores.

Para contas de utilizadores apenas na nuvem, os utilizadores devem alterar as suas palavras-passe antes de poderem utilizar o Azure AD DS. Este processo de alteração de palavra-passe faz com que as hashes de senha para a autenticação Kerberos e NTLM sejam geradas e armazenadas em Azure AD.

Para os utilizadores sincronizados a partir de um ambiente AD DS no local utilizando o Azure AD Connect, ative a [sincronização dos hashes de senha][hybrid-phs].

> [!IMPORTANT]
> O Azure AD Connect apenas sincroniza hashes de senha sincronia quando ativa o Azure AD DS para o seu inquilino Azure AD. As hashes de senha sintetizantes não são usadas se utilizar apenas o Azure AD Connect para sincronizar um ambiente AD DS no local com a AD Azure.
>
> Se as suas aplicações antigas não utilizarem a autenticação NTLM ou os simples encaixes LDAP, recomendamos que desative a sincronização de hash de senha NTLM para DS AD Azure. Para obter mais informações, consulte [Desativar as suites de cifra fraca e a sincronização de hash credencial NTLM][secure-domain].

Uma vez configurados adequadamente, as hashes de senha utilizáveis são armazenadas no domínio gerido pelo Azure AD DS. Se eliminar o domínio gerido pelo Azure AD DS, também são eliminados quaisquer hashes de senha armazenados nessa altura. As informações credenciais sincronizadas em Azure AD não podem ser reutilizadas se criar mais tarde um domínio gerido por Azure AD DS - tem de reconfigurar a sincronização de hash de palavra-passe para armazenar novamente as hashes de senha. Os VMs ou utilizadores anteriormente associados ao domínio não poderão autenticar imediatamente - o Azure AD precisa de gerar e armazenar as hashes de senha no novo domínio gerido pela AD DS do Azure. Para mais informações, consulte o processo de sincronização de [hash password para Azure AD DS e Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio gerido pelo Azure AD DS para sincronizar objetos de volta ao Azure AD.

## <a name="forests-and-trusts"></a>Florestas e confianças

Uma *floresta* é uma construção lógica usada pelos Serviços de Domínio de Diretório Ativo (AD DS) para agrupar um ou mais *domínios.* Os domínios armazenam então objetos para utilizadores ou grupos e fornecem serviços de autenticação.

Em Azure AD DS, a floresta contém apenas um domínio. As florestas AD DS no local contêm frequentemente muitos domínios. Nas grandes organizações, especialmente após fusões e aquisições, você pode acabar com múltiplas florestas no local que cada uma contém vários domínios.

Por padrão, um domínio gerido por Azure AD DS é criado como uma floresta *de utilizadores.* Este tipo de floresta sincroniza todos os objetos da AD Azure, incluindo quaisquer contas de utilizador criadas num ambiente AD DS no local. As contas de utilizador podem autenticar diretamente contra o domínio gerido pelo Azure AD DS, como iniciar sessão num VM de domínio. Uma floresta de utilizadores funciona quando as hashes de senha podem ser sincronizadas e os utilizadores não estão a usar métodos exclusivos de entrada como a autenticação de cartões inteligentes.

Numa floresta de *recursos* Azure AD DS, os utilizadores autenticam sobre uma *confiança* florestal de sentido único a partir do seu DS A.DS no local. Com esta abordagem, os objetos de utilizador e os hashes de senha não são sincronizados com O DS Azure. Os objetos e credenciais do utilizador só existem no DS AD no local. Esta abordagem permite que as empresas acolhem recursos e plataformas de aplicação em Azure que dependem da autenticação clássica como LDAPS, Kerberos ou NTLM, mas quaisquer problemas ou preocupações de autenticação são removidos. As florestas de recursos Azure AD DS estão atualmente em pré-visualização.

Para obter mais informações sobre tipos florestais em Azure AD DS, veja [O que são as florestas][concepts-forest] de recursos? [][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKUs

No Azure AD DS, o desempenho e as funcionalidades disponíveis são baseados no SKU. Selecione um SKU quando criar o domínio gerido e pode mudar as SKUs à medida que os seus requisitos de negócio mudam após a implementação do domínio gerido. O quadro seguinte descreve as SKUs disponíveis e as diferenças entre elas:

| Nome SKU   | Contagem máxima de objetos | Frequência de cópia de segurança | Número máximo de fundos florestais de saída |
|------------|----------------------|------------------|----|
| Standard   | Ilimitado            | A cada 7 dias     | 0  |
| Enterprise | Ilimitado            | A cada 3 dias     | 5  |
| Premium    | Ilimitado            | Diárias            | 10 |

Antes destes Azure AD DS SKUs, foi utilizado um modelo de faturação baseado no número de objetos (contas de utilizador e computador) no domínio gerido pelo Azure AD DS. Já não existe preços variáveis baseados no número de objetos no domínio gerido.

Para mais informações, consulte a página de preços do [Azure AD DS][pricing].

### <a name="managed-domain-performance"></a>Desempenho de domínio gerido

O desempenho do domínio varia com base na forma como a autenticação é implementada para uma aplicação. Recursos computacionais adicionais podem ajudar a melhorar o tempo de resposta à consulta e reduzir o tempo gasto em operações de sincronização. À medida que o nível sku aumenta, os recursos de computação disponíveis para o domínio gerido são aumentados. Monitorize o desempenho das suas aplicações e planeie os recursos necessários.

Se o seu negócio ou aplicação exigir alterações e precisar de uma potência de computação adicional para o seu domínio gerido pelo Azure AD DS, pode mudar para um SKU diferente.

### <a name="backup-frequency"></a>Frequência de cópia de segurança

A frequência de reserva determina com que frequência é tomada uma imagem instantânea do domínio gerido. Backups são um processo automatizado gerido pela plataforma Azure. Em caso de problema com o seu domínio gerido, o suporte do Azure pode ajudá-lo a restaurar a partir de backup. Como a sincronização ocorre apenas de uma forma *a partir de* Azure AD, quaisquer problemas num domínio gerido por Azure AD DS não afetarão os ambientes e funcionalidades AD DS Azure ou no local.

À medida que o nível de SKU aumenta, a frequência desses instantâneos de reserva aumenta. Reveja os seus requisitos de negócio e objetivo de ponto de recuperação (RPO) para determinar a frequência de backup necessária para o seu domínio gerido. Se os seus requisitos de negócio ou aplicação mudarem e precisar de backups mais frequentes, pode mudar para um SKU diferente.

### <a name="outbound-forests"></a>Florestas de saída

A secção anterior detalhou os fundos florestais de saída unidirecionais de um DS da AD DS azure gerido para um ambiente AD DS no local (atualmente em pré-visualização). O SKU determina o número máximo de fundos florestais que pode criar para um domínio gerido pelo Azure AD DS. Reveja os requisitos de negócio e aplicação para determinar quantas fidedignidades realmente precisa e escolha o Azure AD DS SKU apropriado. Mais uma vez, se os seus requisitos de negócio mudarem e precisar de criar fundos florestais adicionais, pode mudar para um SKU diferente.

## <a name="next-steps"></a>Passos seguintes

Para começar, [crie um domínio gerido por Azure AD DS.][create-instance]

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
