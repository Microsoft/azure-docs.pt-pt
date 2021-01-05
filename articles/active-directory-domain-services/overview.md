---
title: Visão geral dos Serviços de Domínio do Diretório Ativo Azure / Microsoft Docs
description: Nesta visão geral, saiba o que a Azure Ative Directory Domain Services fornece e como usá-lo na sua organização para fornecer serviços de identidade a aplicações e serviços na nuvem.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2020
ms.author: justinha
ms.custom: contperf-fy21q1
ms.openlocfilehash: 765aa10e57c472d7a1af0952364783bb1d711078
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861511"
---
# <a name="what-is-azure-active-directory-domain-services"></a>O que é o Azure Active Directory Domain Services?

A Azure Ative Directory Domain Services (AD DS) fornece serviços de domínio geridos, tais como a adesão de domínio, a política de grupo, o protocolo de acesso ao diretório leve (LDAP) e a autenticação Kerberos/NTLM. Utiliza estes serviços de domínio sem a necessidade de implantar, gerir e remendar controladores de domínio (DCs) na nuvem.

Um domínio gerido pela Azure AD DS permite executar aplicações antigas na nuvem que não podem usar métodos modernos de autenticação, ou onde você não quer que as imagens de diretórios voltem sempre a um ambiente AD DS no local. Você pode levantar e deslocar essas aplicações antigas do seu ambiente no local para um domínio gerido, sem precisar de gerir o ambiente AD DS na nuvem.

A Azure AD DS integra-se com o seu inquilino AZure AD existente. Esta integração permite que os utilizadores inscrevam-se no serviço e aplicações ligadas ao domínio gerido utilizando as suas credenciais existentes. Também pode utilizar grupos e contas de utilizador existentes para garantir o acesso aos recursos. Estas características proporcionam um levantamento e mudança mais suaves dos recursos no local para Azure.

> [!div class="nextstepaction"]
> [Para começar, crie um domínio gerido Azure AD DS utilizando o portal Azure][tutorial-create]

## <a name="how-does-azure-ad-ds-work"></a>Como funciona o Azure AD DS?

Quando cria um domínio gerido AZure AD DS, define um espaço de nome único. Este espaço de nome é o nome de domínio, como *aaddscontoso.com*. Dois controladores de domínio do Windows Server (DCs) são então implantados na região de Azure selecionada. Esta implantação de DCs é conhecida como um conjunto de réplicas.

Não precisa de gerir, configurar ou atualizar estes DCs. A plataforma Azure trata os DCs como parte do domínio gerido, incluindo cópias de segurança e encriptação em repouso usando encriptação de disco Azure.

Um domínio gerido é configurado para realizar uma sincronização unidirecional a partir de Azure AD para fornecer acesso a um conjunto central de utilizadores, grupos e credenciais. Pode criar recursos diretamente no domínio gerido, mas não são sincronizados de volta ao Azure AD. Aplicações, serviços e VMs em Azure que se conectam ao domínio gerido podem então usar funcionalidades comuns de DS AD tais como a adesão de domínio, política de grupo, LDAP e a autenticação Kerberos /NTLM.

Num ambiente híbrido com um ambiente AD DS no local, [o Azure AD Connect][azure-ad-connect] sincroniza a informação de identidade com o Azure AD, que é então sincronizado com o domínio gerido.

![Sincronização em Serviços de Domínio AD AD Azure com AZure AD e AD DS no local usando AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

A Azure AD DS replica informações de identidade da Azure AD, por isso trabalha com inquilinos AD AZure que são apenas cloud, ou sincronizados com um ambiente AD DS no local. O mesmo conjunto de funcionalidades AD DS Azure existe para ambos os ambientes.

* Se tiver um ambiente AD DS existente no local, pode sincronizar as informações da conta do utilizador para fornecer uma identidade consistente para os utilizadores. Para saber mais, veja [como os objetos e credenciais são sincronizados num domínio gerido.][synchronization]
* Para ambientes apenas em nuvem, você não precisa de um ambiente tradicional no local AD DS para usar os serviços de identidade centralizados de Azure AD DS.

Você pode expandir um domínio gerido para ter mais de um conjunto de réplicas por inquilino AZure AD. Os conjuntos de réplicas podem ser adicionados a qualquer rede virtual esprevada em qualquer região do Azure que suporte Azure AD DS. Conjuntos de réplicas adicionais em diferentes regiões de Azure proporcionam recuperação geográfica de desastres para aplicações antigas se uma região de Azure ficar offline. Os conjuntos de réplicas estão atualmente em pré-visualização. Para obter mais informações, consulte [replica sets conceitos e funcionalidades para domínios geridos.][concepts-replica-sets]

O vídeo que se segue fornece uma visão geral de como o Azure AD DS se integra com as suas aplicações e cargas de trabalho para fornecer serviços de identidade na nuvem:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

Para ver os cenários de implementação da AD DS em ação, pode explorar os seguintes exemplos:

* [Azure AD DS para organizações híbridas](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS para organizações só em nuvem](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Funcionalidades e benefícios do Azure AD DS

Para fornecer serviços de identidade a aplicações e VMs na nuvem, o Azure AD DS é totalmente compatível com um ambiente tradicional de DS AD para operações como união de domínios, LDAP seguro (LDAPS), Política de Grupo, gestão de DNS e suporte de ligação e leitura LDAP. O suporte de escrita LDAP está disponível para objetos criados no domínio gerido, mas não recursos sincronizados a partir de Azure AD.

Para saber mais sobre as suas opções de identidade, [compare Azure AD DS com Azure AD, AD DS em VMs Azure e AD DS no local][compare].

As seguintes características da Azure AD DS simplificam as operações de implantação e gestão:

* **Experiência de implementação simplificada:** O Azure AD DS está ativado para o seu inquilino AZure AD usando um único assistente no portal Azure.
* **Integrado com Azure AD:** As contas de utilizador, as filiações do grupo e as credenciais estão automaticamente disponíveis no seu inquilino AZure AD. Novos utilizadores, grupos ou alterações a atributos do seu inquilino AZure AD ou do ambiente AD DS no local são automaticamente sincronizados para Azure AD DS.
    * As contas em diretórios externos ligados ao seu AD Azure não estão disponíveis em Azure AD DS. As credenciais não estão disponíveis para os diretórios externos, por isso não podem ser sincronizadas num domínio gerido.
* **Use as suas credenciais/senhas corporativas:** As palavras-passe para utilizadores em Azure AD DS são as mesmas que no seu inquilino AZure AD. Os utilizadores podem usar as suas credenciais corporativas para máquinas de união de domínios, assinar em ambientes de trabalho interativos ou sobre ambientes de trabalho remotos e autenticar contra o domínio gerido.
* **Autenticação NTLM e Kerberos:** Com suporte para a autenticação NTLM e Kerberos, pode implementar aplicações que dependam da autenticação integrada do Windows.
* **Alta disponibilidade:** O Azure AD DS inclui vários controladores de domínio, que fornecem alta disponibilidade para o seu domínio gerido. Esta elevada disponibilidade garante o tempo de serviço e a resiliência às falhas.
    * Nas regiões que suportam [zonas de disponibilidade de Azure,][availability-zones]estes controladores de domínio também são distribuídos por zonas para maior resiliência.
    * [Os conjuntos de réplicas][concepts-replica-sets] também podem ser usados para fornecer recuperação geográfica de desastres para aplicações antigas se uma região de Azure ficar offline.

Alguns aspectos-chave de um domínio gerido incluem o seguinte:

* O domínio gerido é um domínio autónomo. Não é uma extensão de um domínio no local.
    * Se necessário, você pode criar fundos florestais de ida e saída da Azure AD DS para um ambiente AD DS no local. Para obter mais informações, consulte [conceitos e funcionalidades da floresta de recursos para Azure AD DS.][ forest-trusts]
* A sua equipa de TI não precisa de gerir, corrigir ou monitorizar controladores de domínio para este domínio gerido.

Para ambientes híbridos que executam AD DS no local, você não precisa gerir a replicação de AD para o domínio gerido. As contas de utilizador, os membros do grupo e as credenciais do seu diretório no local são sincronizados para Azure AD via [Azure AD Connect][azure-ad-connect]. Estas contas de utilizador, membros do grupo e credenciais estão automaticamente disponíveis dentro do domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Azure AD DS compara-se com outras soluções de identidade e como funciona a sincronização, consulte os seguintes artigos:

* [Compare Azure AD DS com Azure AD, Ative Directory Domain Services em Azure VMs e Ative Directory Domain Services no local][compare]
* [Saiba como o Azure AD Domain Services sincroniza com o seu diretório AD Azure][synchronization]
* Para aprender a administrar um domínio gerido, consulte [conceitos de gestão para contas de utilizador, palavras-passe e administração em Azure AD DS][administration-concepts].

Para começar, [crie um domínio gerido utilizando o portal Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
