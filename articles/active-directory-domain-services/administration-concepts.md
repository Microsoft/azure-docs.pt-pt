---
title: Conceitos de gestão para Azure AD Domain Services / Microsoft Docs
description: Saiba como administrar um domínio gerido pelo Azure Ative Directory Domain Services e o comportamento das contas de utilizador e das palavras-passe
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: iainfou
ms.openlocfilehash: 6266248b817485562c7ed2643b3dda5f32cecc53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87489678"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Conceitos de gestão para contas de utilizador, palavras-passe e administração em Azure Ative Directory Domain Services

Quando cria e gere um domínio gerido por Azure Ative Directory Domain Services (AD DS), existem algumas diferenças de comportamento em comparação com um ambiente tradicional de DS AD no local. Utiliza as mesmas ferramentas administrativas em Azure AD DS como um domínio auto-gerido, mas não pode aceder diretamente aos controladores de domínio (DC). Há também algumas diferenças de comportamento para as políticas de senha e hashes de senha dependendo da origem da criação da conta de utilizador.

Este artigo conceptual detalha como administrar um domínio gerido e o comportamento diferente das contas de utilizador dependendo da forma como são criadas.

## <a name="domain-management"></a>Gestão de domínios

Um domínio gerido é um espaço de nome DNS e diretório correspondente. Num domínio gerido, os controladores de domínio (DCs) que contêm todos os recursos, como utilizadores e grupos, credenciais e políticas fazem parte do serviço gerido. Para redundância, dois DCs são criados como parte de um domínio gerido. Não pode inscrever-se nestes DCs para executar tarefas de gestão. Em vez disso, cria um VM de gestão que se juntou ao domínio gerido e, em seguida, instalou as suas ferramentas regulares de gestão de DS AD. Pode utilizar o Ative Directory Administrative Center ou o Microsoft Management Console (MMC) como os objetos DNS ou Política de Grupo, por exemplo.

## <a name="user-account-creation"></a>Criação de conta de utilizador

As contas de utilizador podem ser criadas num domínio gerido de várias formas. A maioria das contas de utilizadores são sincronizadas a partir do Azure AD, que também pode incluir a conta de utilizador sincronizada a partir de um ambiente AD DS no local. Também pode criar manualmente contas diretamente no domínio gerido. Algumas funcionalidades, como a sincronização inicial da palavra-passe ou a política de palavra-passe, comportam-se de forma diferente consoante a forma e onde as contas de utilizador são criadas.

* A conta de utilizador pode ser sincronizada a partir do Azure AD. Isto inclui contas de utilizadores criadas apenas na nuvem criadas diretamente no AZure AD, e contas híbridas de utilizadores sincronizadas a partir de um ambiente AD DS no local usando Azure AD Connect.
    * A maioria das contas de utilizador num domínio gerido são criadas através do processo de sincronização a partir do Azure AD.
* A conta do utilizador pode ser criada manualmente num domínio gerido, e não existe em Azure AD.
    * Se precisar de criar contas de serviço para aplicações que só funcionam no domínio gerido, pode criá-las manualmente no domínio gerido. Como a sincronização está a um caminho do Azure AD, as contas de utilizador criadas no domínio gerido não são sincronizadas de volta ao AZure AD.

## <a name="password-policy"></a>Política de senha

O Azure AD DS inclui uma política de senha padrão que define definições para coisas como bloqueio de conta, idade máxima de senha e complexidade da palavra-passe. As configurações como a política de bloqueio de conta aplicam-se a todos os utilizadores num domínio gerido, independentemente da forma como o utilizador foi criado como descrito na secção anterior. Algumas configurações, como o comprimento mínimo da palavra-passe e a complexidade da palavra-passe, aplicam-se apenas aos utilizadores criados diretamente num domínio gerido.

Pode criar as suas próprias políticas de palavra-passe personalizadas para anular a política por defeito num domínio gerido. Estas políticas personalizadas podem então ser aplicadas a grupos específicos de utilizadores, se necessário.

Para obter mais informações sobre as diferenças na forma como as políticas de palavra-passe são aplicadas em função da origem da criação do utilizador, consulte as políticas de bloqueio de [passwords e contas em domínios geridos][password-policy].

## <a name="password-hashes"></a>Hashes password

Para autenticar os utilizadores no domínio gerido, o Azure AD DS necessita de hashes de senha num formato adequado para a autenticação do NT LAN Manager (NTLM) e da autenticação Kerberos. O Azure AD não gera ou armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até ativar o Azure AD DS para o seu inquilino. Por razões de segurança, a Azure AD também não armazena quaisquer credenciais de senha em formato de texto claro. Portanto, o Azure AD não pode gerar automaticamente estes hashes de palavra-passe NTLM ou Kerberos com base nas credenciais existentes dos utilizadores.

Para contas de utilizadores apenas na nuvem, os utilizadores devem alterar as suas palavras-passe antes de poderem utilizar o domínio gerido. Este processo de alteração de palavra-passe faz com que os hashes de palavra-passe para a autenticação Kerberos e NTLM sejam gerados e armazenados em Azure AD. A conta não é sincronizada de Azure AD para Azure AD DS até que a palavra-passe seja alterada.

Para os utilizadores sincronizados a partir de um ambiente AD DS no local utilizando o Azure AD Connect, [permitir a sincronização dos hashes de palavra-passe][hybrid-phs].

> [!IMPORTANT]
> O Azure AD Connect apenas sincroniza as hashes de senhas antigas quando ativa o Azure AD DS para o seu inquilino AZure AD. Os hashes de senha legado não são usados se você apenas usar Azure AD Connect para sincronizar um ambiente AD DS no local com Azure AD.
>
> Se as suas aplicações antigas não utilizarem a autenticação NTLM ou ligações simples LDAP, recomendamos que desative a sincronização de hash de palavra-passe NTLM para Azure AD DS. Para obter mais informações, consulte [Desativar as suítes de cifra fracas e a sincronização de haxixe credencial NTLM][secure-domain].

Uma vez configurados de forma adequada, os hashes de palavra-passe utilizáveis são armazenados no domínio gerido. Se eliminar o domínio gerido, qualquer hashes de palavra-passe armazenados nesse ponto também são eliminados. As informações de credenciais sincronizadas no Azure AD não podem ser reutilizadas se mais tarde criar outro domínio gerido - tem de reconfigurar a sincronização de hash da palavra-passe para armazenar novamente o hashes da palavra-passe. VMs ou utilizadores previamente unidos ao domínio não poderão autenticar imediatamente - A Azure AD precisa gerar e armazenar as hashes de palavra-passe no novo domínio gerido. Para obter mais informações, consulte [o processo de sincronização de haxixe password para Azure AD DS e Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio gerido para sincronizar objetos de volta ao AZure AD.

## <a name="forests-and-trusts"></a>Florestas e confianças

Uma *floresta* é uma construção lógica usada pelos Ative Directory Domain Services (AD DS) para agrupar um ou mais *domínios*. Os domínios armazenam então objetos para utilizador ou grupos e fornecem serviços de autenticação.

Em Azure AD DS, a floresta contém apenas um domínio. As florestas de DS AD no local muitas vezes contêm muitos domínios. Em grandes organizações, especialmente após fusões e aquisições, você pode acabar com múltiplas florestas no local que cada uma então contém vários domínios.

Por padrão, um domínio gerido é criado como uma floresta *de utilizadores.* Este tipo de floresta sincroniza todos os objetos a partir de Azure AD, incluindo quaisquer contas de utilizador criadas em um ambiente AD DS no local. As contas dos utilizadores podem autenticar-se diretamente contra o domínio gerido, de modo a iniciar seducação num VM de domínio. Uma floresta de utilizadores funciona quando os hashes de palavra-passe podem ser sincronizados e os utilizadores não estão a usar métodos exclusivos de entrada como a autenticação de cartões inteligentes.

Numa floresta de *recursos* Azure AD DS, os utilizadores autenticam-se sobre uma *confiança* florestal unidireccionária a partir dos seus DS AD no local. Com esta abordagem, os objetos de utilizador e as hashes de palavra-passe não são sincronizados com O AZure AD DS. Os objetos e credenciais do utilizador só existem no local AD DS. Esta abordagem permite às empresas acolher recursos e plataformas de aplicação em Azure que dependem da autenticação clássica como LDAPS, Kerberos ou NTLM, mas quaisquer problemas ou preocupações de autenticação são removidos.

Para obter mais informações sobre os tipos de floresta em Azure AD DS, veja [o que são as florestas de recursos?][concepts-forest] [How do forest trusts work in Azure AD DS?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKUs

No Azure AD DS, o desempenho e funcionalidades disponíveis são baseados no SKU. Selecione um SKU quando criar o domínio gerido e pode mudar o SKUs à medida que os seus requisitos de negócio mudam após a implementação do domínio gerido. O quadro que se segue descreve os SKUs disponíveis e as diferenças entre eles:

| Nome SKU   | Contagem máxima de objetos | Frequência de cópia de segurança | Número máximo de fundos florestais de saída |
|------------|----------------------|------------------|----|
| Standard   | Ilimitado            | A cada 7 dias     | 0  |
| Grandes Empresas | Ilimitado            | A cada 3 dias     | 5  |
| Premium    | Ilimitado            | Diário            | 10 |

Antes desteS Azure AD DS SKUs, foi utilizado um modelo de faturação baseado no número de objetos (contas de utilizador e computador) no domínio gerido. Já não existe preços variáveis com base no número de objetos no domínio gerido.

Para mais informações, consulte a página de preços da [Azure AD DS][pricing].

### <a name="managed-domain-performance"></a>Desempenho de domínio gerido

O desempenho do domínio varia em com base na forma como a autenticação é implementada para uma aplicação. Recursos adicionais de computação podem ajudar a melhorar o tempo de resposta à consulta e reduzir o tempo gasto em operações de sincronização. À medida que o nível SKU aumenta, os recursos de computação disponíveis para o domínio gerido são aumentados. Monitorize o desempenho das suas aplicações e planeie os recursos necessários.

Se o seu negócio ou aplicação exigir mudanças e precisar de energia de computação adicional para o seu domínio gerido, pode mudar para um SKU diferente.

### <a name="backup-frequency"></a>Frequência de cópia de segurança

A frequência de backup determina a frequência com que é tirada uma imagem do domínio gerido. As cópias de segurança são um processo automatizado gerido pela plataforma Azure. Em caso de problema com o seu domínio gerido, o suporte Azure pode ajudá-lo a restaurar a partir de backup. Como a sincronização ocorre apenas de uma maneira *a partir do* Azure AD, quaisquer problemas num domínio gerido não impactarão ambientes e funcionalidades AD AD no local.

À medida que o nível de SKU aumenta, a frequência desses instantâneos de backup aumenta. Reveja os requisitos do seu negócio e o objetivo do ponto de recuperação (RPO) para determinar a frequência de backup necessária para o seu domínio gerido. Se os requisitos de negócio ou aplicação mudarem e precisar de cópias de segurança mais frequentes, pode mudar para um SKU diferente.

### <a name="outbound-forest-trusts"></a>Fundos florestais de saída

A secção anterior detalhava os fundos florestais de saída de uma só ida de um domínio gerido para um ambiente DS AD no local. O SKU determina o número máximo de fundos florestais que pode criar para um domínio gerido. Reveja os requisitos de negócio e aplicação para determinar quantos fundos precisa, e escolha o Azure AD DS SKU apropriado. Mais uma vez, se os requisitos do seu negócio mudarem e precisar de criar fundos florestais adicionais, pode mudar para um SKU diferente.

## <a name="next-steps"></a>Passos seguintes

Para começar, [crie um domínio gerido Azure AD DS][create-instance].

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
