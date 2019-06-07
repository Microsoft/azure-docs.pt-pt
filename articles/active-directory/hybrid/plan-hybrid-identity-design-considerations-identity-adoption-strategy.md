---
title: Design de identidade híbrida - estratégia de adoção do Azure | Documentos da Microsoft
description: Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas que escolher quando está a autenticar o utilizador e antes de permitir o acesso à aplicação. Assim que essas condições são cumpridas, o utilizador é autenticado e permissão para aceder à aplicação.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c55dc0cbc175542d0b208bbe72566a0adb55a371
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496730"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definir uma estratégia de adoção de identidade híbrida
Nesta tarefa, vai definir a estratégia de adoção de identidade híbrida para a sua solução de identidade híbrida satisfazer os requisitos empresariais que foram abordados em:

* [Determinar as necessidades de negócio](plan-hybrid-identity-design-considerations-business-needs.md)
* [Determinar requisitos de sincronização de diretórios](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Determinar os requisitos de autenticação multifator](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definir a estratégia de necessidades de negócio
Os endereços de tarefa primeiro determinar os negócios de organizações precisa.  Isso pode ser muito amplo e ampliação do escopo pode ocorrer se não for cuidadoso.  No início, manter a simplicidade mas lembre-se sempre ao planear uma conceção que acomodar e facilite a mudança no futuro.  Independentemente de ser um design simples ou um extremamente complexo, Azure Active Directory é a plataforma do Microsoft Identity que suporta o Office 365, Microsoft Online Services e na cloud com suporte para aplicações.

## <a name="define-an-integration-strategy"></a>Definir uma estratégia de integração
A Microsoft tem três cenários de integração principais que são identidades federadas, identidades sincronizadas e identidades de nuvem.  Deve planejar a adoção de uma destas estratégias de integração.  A estratégia que escolher pode variar e podem incluir as decisões de escolher um, que tipo de experiência de utilizador que pretende fornecer, tem uma infraestrutura existente, e o que é a mais econômica.  

![Cenários de integração](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Os cenários definidos na figura acima são:

* **Identidades de nuvem**: estes são identidades que existem apenas na nuvem.  No caso do Azure AD, eles residem especificamente no diretório do Azure AD.
* **Sincronizadas**: estes são identidades existentes no local e na cloud.  Utilizando o Azure AD Connect, estes utilizadores estão criados ou associados a contas existentes do Azure AD.  Hash de palavra-passe do utilizador é sincronizado a partir do ambiente no local para a cloud em que é chamado um hash de palavra-passe.  Quando utilizar sincronizado a única limitação é que se um utilizador é desativado no ambiente no local, pode demorar até três horas para que o estado da conta a aparecer no Azure AD.  Isso se deve o intervalo de tempo de sincronização.
* **Federado**: existem destas identidades no local e na cloud.  Utilizando o Azure AD Connect, estes utilizadores estão criados ou associados a contas existentes do Azure AD.  

> [!NOTE]
> Para obter mais informações sobre as opções de sincronização, leia [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

A tabela a seguir ajuda a determinar as vantagens e desvantagens de cada uma das seguintes estratégias:

| Estratégia | Vantagens | Desvantagens |
| --- | --- | --- |
| **Identidades de nuvem** |Mais fácil de gerenciar para pequena organização. <br> Nada para instalar no local. Sem hardware adicional necessária<br>Facilmente desativada se o utilizador sai da empresa |Os utilizadores terão de iniciar sessão, ao aceder a cargas de trabalho na cloud <br> As palavras-passe podem ou não ser o mesmo para as identidades de nuvem e no local |
| **Sincronizados** |No local é autenticado uma palavra-passe no local e na cloud diretórios <br>Mais fácil de gerenciar para pequena, média ou grande porte <br>Os usuários podem ter início de sessão único (SSO) para alguns recursos <br> Método da Microsoft preferida para sincronização <br> Mais fácil de gerenciar |Alguns clientes podem estar relutantes em Sincronizar seus diretórios com a cloud devido a polícia específicas da empresa |
| **Federado** |Os usuários podem ter início de sessão único (SSO) <br>Se um utilizador é terminado ou sai, a conta pode ser desabilitada imediatamente e acesso revogado,<br> Suporta cenários que não podem ser realizados com avançados sincronizados |Obter mais passos para preparar e configurar <br> Manutenção superior <br> Podem exigir hardware adicional para a infraestrutura de STS <br> Podem exigir hardware adicional para instalar o servidor de Federação. Software adicional é necessário se for utilizado o AD FS <br> Exigir configuração extensiva para SSO <br> Ponto crítico de falha se o servidor de Federação está inativo, os utilizadores não será possível efetuar a autenticação |

### <a name="client-experience"></a>Experiência do cliente
A estratégia que utilizar imponha a experiência de início de sessão do utilizador.  As tabelas seguintes fornecem informações sobre quais os utilizadores devem esperar que a sua experiência de início de sessão para ser.  Nem todos os fornecedores de identidade federada suportam o SSO em todos os cenários.

**Aplicativos de rede associados a um domínio e privadas**:

|  | Identidades sincronizadas | Identidades federadas |
| --- | --- | --- |
| Navegadores da Web |Autenticação baseada em formulários |início de sessão único, por vezes, deve fornecer o ID da organização |
| Outlook |Pedir credenciais |Pedir credenciais |
| Skype para empresas (Lync) |Pedir credenciais |início de sessão único para o Lync, lhe for pedido credenciais para o Exchange |
| OneDrive para Empresas |Pedir credenciais |Início de sessão único |
| Office Pro Plus subscrição |Pedir credenciais |Início de sessão único |

**Origens externas ou não fidedignas**:

|  | Identidades sincronizadas | Identidades federadas |
| --- | --- | --- |
| Navegadores da Web |Autenticação baseada em formulários |Autenticação baseada em formulários |
| Outlook, Skype para empresas (Lync), OneDrive para empresas, a subscrição do Office |Pedir credenciais |Pedir credenciais |
| Exchange ActiveSync |Pedir credenciais |início de sessão único para o Lync, lhe for pedido credenciais para o Exchange |
| Aplicações móveis |Pedir credenciais |Pedir credenciais |

Se o ter determinado na tarefa 1, que tem um terceiro IdP ou são vai utilizar uma para fornecer o federação com o Azure AD, tem de ter em consideração as seguintes capacidades suportadas:

* Qualquer fornecedor de SAML 2.0 que está em conformidade para o perfil de SP Lite pode suportar a autenticação para o Azure AD e associados a aplicativos
* Suporta autenticação passiva, o que facilita a autenticação para OWA, SPO, etc.
* Clientes do Exchange Online podem ser suportados através de SAML 2.0 avançada cliente Perfil (ECP)

Também pode estar ciente de que capacidades não estarão disponíveis:

* Sem o suporte de WS-Trust/Federation, interromper todos os outros clientes ativos
  * Isso significa que nenhum cliente do Lync, o cliente do OneDrive, a subscrição do Office, o Office Mobile antes do Office 2016
* Transição do Office para autenticação passiva permite que essas para suporte de IdPs de 2.0 pura SAML, mas o suporte ainda estará numa base por cliente

> [!NOTE]
> Para a lista mais atualizada, leia o artigo [lista de compatibilidades de Federação do Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definir a estratégia de sincronização
Nesta tarefa, vai definir as ferramentas que serão utilizadas para sincronizar a organização dados no local para a cloud e o que deve usar de topologia.  Porque, na maioria das organizações utiliza o Active Directory, são fornecidas informações sobre como utilizar o Azure AD Connect para lidar com perguntas acima em alguns detalhes.  Para ambientes que não têm do Active Directory, há informações sobre como utilizar o FIM 2010 R2 ou o MIM 2016 para ajudar a planear esta estratégia.  No entanto, as versões futuras do Azure AD Connect irão suportar diretórios LDAP, por isso, dependendo da sua linha cronológica, esta informação pode ser capaz de ajudá-lo.

### <a name="synchronization-tools"></a>Ferramentas de sincronização
Ao longo dos anos, várias ferramentas de sincronização tem existia e utilizado para vários cenários.  Atualmente o Azure AD Connect é de ir para a ferramenta preferencial para todos os cenários suportados.  AAD Sync e DirSync também ainda estão em todo e podem até ser presentes no seu ambiente agora. 

> [!NOTE]
> Para obter as informações mais recentes sobre as capacidades suportadas de cada ferramenta, leia [comparação das ferramentas de integração de diretórios](plan-hybrid-identity-design-considerations-tools-comparison.md) artigo.  
> 
> 

### <a name="supported-topologies"></a>Topologias suportadas
Ao definir uma estratégia de sincronização, tem de ser determinada a topologia que é utilizada. Consoante as informações de que foi determinadas no passo 2 é possível determinar a topologia é a correta para utilizar. A única floresta, única consiste numa única floresta do Active Directory e uma única instância do Azure AD e é o mais comum de topologia do AD do Azure.  Isso vai ser utilizada numa maioria dos cenários e a topologia esperada ao utilizar a instalação do Azure AD Connect Express conforme mostrado na figura abaixo.

![Suportadas topologias](./media/plan-hybrid-identity-design-considerations/single-forest.png) única floresta cenário é comum para as pequenas e grandes organizações de várias florestas, conforme mostrado na figura 5.

> [!NOTE]
> Para obter mais informações sobre os diferentes no local e topologias do Azure AD com o Azure AD Connect sync leia o artigo [topologias do Azure AD Connect](plan-connect-topologies.md).
> 
> 

![topologia de várias floresta](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Cenário de várias floresta

Se este for o caso, em seguida, a única de várias floresta topologia do AD do Azure deve ser considerada se os itens seguintes forem verdadeiros:

* Os utilizadores têm apenas 1 identidade em todas as florestas – a secção de utilizadores exclusivamente identificação abaixo descreve isso mais detalhadamente.
* O utilizador é autenticado na floresta em que se encontra a identidade
* UPN e a âncora de origem (id imutável) serão provenientes desta floresta
* Todas as florestas estão acessíveis pelo Azure AD Connect – Isto significa que ele não precisa ser o domínio associado e pode ser colocado numa rede de Perímetro, se isso facilita isso.
* Os utilizadores têm apenas uma caixa de correio
* A floresta que hospeda a caixa de correio de um utilizador tem a melhor qualidade de dados de atributos visíveis na lista Exchange da endereço Global (GAL)
* Se não houver nenhuma caixa de correio no utilizador, pode ser utilizada qualquer floresta, em seguida, estes valores de contribuir
* Se tiver uma caixa de correio vinculada, em seguida, também há outra conta numa floresta diferente utilizada para iniciar sessão.

> [!NOTE]
> Objetos que existem em ambos os locais e na cloud "ligados" através de um identificador exclusivo. No contexto de sincronização de diretórios, este identificador exclusivo é referido como o SourceAnchor. No contexto de início de sessão único, isto é referido como o ImmutableId. [Conceitos de design para o Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) para obter mais considerações sobre a utilização de SourceAnchor.
> 
> 

Se o procedimento acima não forem VERDADEIRO e tiver mais de uma conta do Active Directory ou de mais do que uma caixa de correio, o Azure AD Connect escolha uma e ignorar o outro.  Caso de ligação caixas de correio, mas não existe nenhuma outra conta, estas contas não serão exportadas para o Azure AD e que o utilizador não será um membro de nenhum dos grupos.  Isso é diferente de como era no passado com o DirSync e é intencional para melhor suporte esses cenários de várias florestas. Um cenário de várias floresta é mostrado na figura abaixo.

![Vários inquilinos do Azure AD](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Várias florestas vários cenário do Azure AD**

É recomendado ter apenas um único diretório no Azure AD para uma organização, mas é possível que sejam mantida uma relação de 1:1 entre um servidor de sincronização do Azure AD Connect e um diretório do Azure AD.  Para cada instância do Azure AD, tem de ter uma instalação do Azure AD Connect.  Além disso, o Azure AD, por design é isolado e os utilizadores numa única instância do Azure AD não será capazes de ver os utilizadores em outra instância.

É possível e compatível para ligar uma instância no local do Active Directory para vários diretórios do Azure AD, conforme mostrado na figura abaixo:

![filtragem de floresta única](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Cenário de filtragem de floresta única**

Para fazer isso, a seguir deve ser verdadeira:

* Servidores de sincronização do Azure AD Connect tem de ser configurados para filtragem, para cada um tem um conjunto mutuamente exclusivo de objetos.  Isso feito, por exemplo, ao controlo de âmbito de cada servidor a um determinado domínio ou UO.
* Um domínio DNS só pode ser registrado num único diretório do Azure AD por isso, os UPNs AD tem de utilizar espaços de nomes separados dos usuários no local
* Os utilizadores numa única instância do Azure AD apenas poderão ver utilizadores da sua instância.  Eles não será capazes de ver os utilizadores as outras instâncias
* Apenas um dos diretórios do Azure AD pode ativar híbrida do Exchange no local com o AD
* Exclusividade mútua também se aplica a repetição de escrita.  Isto torna algumas funcionalidades de repetição de escrita não são suportadas com essa topologia, uma vez que estes partem do princípio de uma única configuração no local.  Isto inclui:
  * Repetição de escrita de grupo com a configuração predefinida
  * Repetição de escrita do dispositivo

O seguinte não é suportado e não deve ser escolhido como uma implementação:

* Não é suportado ter múltiplos servidores de sincronização do Azure AD Connect ligar-se no mesmo diretório do Azure AD, mesmo se estiverem configurados para sincronizar o conjunto mutuamente exclusivo do objeto
* Ele não é suportado para sincronizar o mesmo utilizador para vários diretórios do Azure AD. 
* Também-não é suportado para fazer uma alteração de configuração para que os usuários num Azure AD para aparecer como contatos de outro diretório do Azure AD. 
* Ele também é suportado para modificar a sincronização do Azure AD Connect para ligar a vários diretórios do Azure AD.
* Diretórios do Azure AD são, por design em ambiente isolado. Isso não é suportado para alterar a configuração da sincronização do Azure AD Connect para ler dados a partir de outro diretório do Azure AD numa tentativa de criar uma GAL comuns e unificada entre os diretórios. Ele também não é suportado a exportação de utilizadores como contatos para outro AD no local através do Azure AD Connect.

> [!NOTE]
> Se sua organização restringe os computadores na sua rede estabeleçam uma ligação à Internet, este artigo lista os pontos de extremidade (FQDN, IPv4 e IPv6 intervalos de endereços) que deve incluir na sua saída permitem que listas e zona de Sites do Internet Explorer fidedigna do cliente computadores para garantir que seus computadores com êxito podem utilizar o Office 365. Para obter mais informações, leia [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definir a estratégia de autenticação multifator
Nesta tarefa, vai definir a estratégia de autenticação multifator para utilizar.  O Azure multi-factor Authentication é fornecido em duas versões diferentes.  Uma é uma conta baseada na cloud e a outra é no local com base através do servidor de MFA do Azure.  Com base na avaliação acima que pode determinar que solução é a correta para sua estratégia.  Utilize a tabela abaixo para determinar que opção de design que melhor cumpre o requisito de segurança da sua empresa:

Opções de design de multi-factor:

| Recursos para proteger | MFA na nuvem | MFA no local |
| --- | --- | --- |
| Aplicações da Microsoft |sim |sim |
| Aplicações SaaS na galeria de aplicações |sim |sim |
| Aplicações IIS publicadas através do Proxy de Aplicação do Azure AD |sim |sim |
| Aplicações de IIS não publicadas através do Proxy de aplicações do Azure AD |não |sim |
| Acesso remoto como VPN, RDG |não |sim |

Embora pode definir uma solução para a sua estratégia, ainda tem de utilizar a avaliação acima no qual estão localizados os seus utilizadores.  Isso pode fazer com que a solução alterar.  Utilize a tabela abaixo para ajudá-lo a determinar isto:

| Localização do utilizador | Opção de conceção preferida |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication na cloud |
| Azure AD e AD no local utilizando federação com o AD FS |Ambos |
| O Azure AD e no local sem sincronização de palavra-passe AD com o Azure AD Connect |Ambos |
| O Azure AD e no local através do Azure AD Connect com a sincronização de palavra-passe |Ambos |
| AD no local |Servidor Multi-Factor Authentication |

> [!NOTE]
> Também deve garantir que a opção de design de autenticação multifator que selecionou suporta as funcionalidades que são necessárias para seu design.  Para obter mais informações, leia [escolher a solução de segurança multifator para si](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Fornecedor do multi-factor Auth
Autenticação multifator está disponível por predefinição para administradores globais que possuam um inquilino do Azure Active Directory. No entanto, se desejar estender a autenticação multifator para todos os seus utilizadores e/ou mesmo para os administradores globais possam tirar partido recursos como o portal de gestão, saudações personalizadas e relatórios, em seguida, deve comprar e configurar Fornecedor de multi-factor Authentication.

> [!NOTE]
> Também deve garantir que a opção de design de autenticação multifator que selecionou suporta as funcionalidades que são necessárias para seu design. 
> 
> 

## <a name="next-steps"></a>Passos Seguintes
[Determinar os requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Consulte também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

