---
title: Rotação de Emergência dos certificados de FS da AD | Microsoft Docs
description: Este artigo explica como revogar e atualizar imediatamente os certificados AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613127"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>Rotação de Emergência dos certificados AD FS
No caso de necessitar de rodar imediatamente os certificados AD FS, pode seguir os passos descritos abaixo nesta secção.

> [!IMPORTANT]
> A realização dos passos abaixo no ambiente das FS AD revogará imediatamente os certificados antigos.  Como isto é feito imediatamente, o tempo normal normalmente permitido para os seus parceiros da federação consumirem o seu novo certificado é passado. Pode resultar numa paragem de serviço como atualização de fundos para usar os novos certificados.  Isto deve resolver-se uma vez que todos os sócios da federação tenham os novos certificados.

> [!NOTE]
> A Microsoft recomenda vivamente a utilização de um Módulo de Segurança de Hardware (HSM) para proteger e proteger certificados.
> Para obter mais informações consulte [o Módulo de Segurança de Hardware](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) sob as melhores práticas para garantir o AD FS.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>Determine a sua impressão digital do certificado de assinatura token
Para revogar o antigo Certificado de Assinatura Token que a AD FS está atualmente a utilizar, é necessário determinar a impressão digital do certificado de token-sigining.  Para isso, utilize os seguintes passos abaixo:

 1. Ligue-se ao Serviço Online da Microsoft `PS C:\>Connect-MsolService`
 2. Documente as datas de impressão digital e de validade do certificado de assinatura token no local e na nuvem.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Copie a impressão digital.  Será usado mais tarde para remover os certificados existentes.

Também pode obter a impressão digital através da Gestão AD FS, navegando para Serviço/Certificados, clicando à direita no certificado, selecione o certificado 'Ver' e, em seguida, selecione Detalhes. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Determinar se a AD FS renova automaticamente os certificados
Por predefinição, o AD FS é configurado para gerar automaticamente certificados de assinatura simbólica e de desencriptação simbólica, tanto na hora inicial de configuração como quando os certificados se aproximam da sua data de validade.

Pode executar o seguinte comando Windows PowerShell: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` .

A propriedade AutoCertificateRollover descreve se a AD FS está configurada para renovar automaticamente os certificados de assinatura simbólica e desencriptação de fichas.  Se o AutoCertateRollover estiver definido para TRUE, siga as instruções descritas abaixo em [Gerar novo certificado auto-assinado se o AutoCertificateRollover estiver definido para TRUE].  Se o AutoCertateRollover estiver definido como FALSO, siga as instruções descritas abaixo em [Gerar novos certificados manualmente se o AutoCertateRollover estiver definido como FALSO]


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Gerar novo certificado auto-assinado se o AutoCertificateRollover estiver definido para TRUE
Nesta secção, você vai criar **dois** certificados de assinatura simbólica.  A primeira utilizará a bandeira **urgente,** que substituirá imediatamente o certificado primário atual.  O segundo será usado para o certificado secundário.  

>[!IMPORTANT]
>A razão pela qual estamos a criar dois certificados é porque a Azure tem informações sobre o certificado anterior.  Ao criar um segundo, estamos forçando a Azure a divulgar informações sobre o antigo certificado e a substituí-la por informações sobre o segundo certificado.
>
>Se não criar o segundo certificado e atualizar o Azure com o mesmo, poderá ser possível que o antigo certificado de assinatura simbólica autua os utilizadores.

Pode utilizar os seguintes passos para gerar os novos certificados de assinatura simbólica.

 1. Certifique-se de que está ligado ao servidor FS AD primário.
 2. Abra o Windows PowerShell Como um administrador. 
 3. Verifique se o seu AutoCertateRollover está definido para True.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Para gerar um novo certificado de assinatura simbólica: `Update-ADFSCertificate –CertificateType token-signing -Urgent` .
 5. Verifique a atualização executando o seguinte comando: `Get-ADFSCertificate –CertificateType token-signing`
 6. Agora gere o segundo certificado de assinatura simbólica: `Update-ADFSCertificate –CertificateType token-signing` .
 7. Pode verificar a atualização executando novamente o seguinte comando: `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Gerar novos certificados manualmente se o AutoCertificateRollover estiver definido como FALSO
Se não estiver a utilizar automaticamente os certificados de assinatura de token gerados automaticamente, auto-assinados e certificados de desencriptação simbólico, deve renovar e configurar estes certificados manualmente.  Isto implica a criação de dois novos certificados de assinatura simbólica e a sua importação.  Depois promove-se um para o primário, revoga o certificado antigo e configura o segundo certificado como certificado secundário.

Em primeiro lugar, deve obter dois novos certificados da sua autoridade de certificados e importá-los na loja de certificados pessoais da máquina local em cada servidor da federação. Para obter instruções, consulte o artigo ['Importar um Certificado'.](https://technet.microsoft.com/library/cc754489.aspx)

>[!IMPORTANT]
>A razão pela qual estamos a criar dois certificados é porque a Azure tem informações sobre o certificado anterior.  Ao criar um segundo, estamos forçando a Azure a divulgar informações sobre o antigo certificado e a substituí-la por informações sobre o segundo certificado.
>
>Se não criar o segundo certificado e atualizar o Azure com o mesmo, poderá ser possível que o antigo certificado de assinatura simbólica autua os utilizadores.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Para configurar um novo certificado como certificado secundário
Em seguida, deve configurar um certificado como o certificado secundário de assinatura ou desencriptação AD FS e, em seguida, promovê-lo para o primário

1. Depois de importar o certificado. Abra a consola **AD FS Management.**
2. Expandir **o Serviço** e, em seguida, selecionar **Certificados**.
3. No painel de ações, clique **em Adicionar Token-Signing Certificate**.
4. Selecione o novo certificado na lista de certificados apresentados e, em seguida, clique em OK.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>Promover o novo certificado do secundário ao primário
Agora que o novo certificado foi importado e configurado em AD FS, você precisa definir como o certificado principal.
1. Abra a consola **AD FS Management.**
2. Expandir **o Serviço** e, em seguida, selecionar **Certificados**.
3. Clique no certificado de assinatura de fichas secundárias.
4. No painel **de ações,** clique **em "set As Primary".** Clique em Sim na solicitação de confirmação.
5. Uma vez que tenha promovido o novo certificado como certificado primário, deverá retirar o certificado antigo porque ainda pode ser utilizado. Consulte a secção [remover os certificados antigos](#remove-your-old-certificates) abaixo.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>Para configurar o segundo certificado como certificado secundário
Agora que adicionou o primeiro certificado e o tornou primário e retirou o antigo, importe o segundo certificado.  Em seguida, deve configurar o certificado como o certificado de assinatura de ficha ad FS secundário

1. Depois de importar o certificado. Abra a consola **AD FS Management.**
2. Expandir **o Serviço** e, em seguida, selecionar **Certificados**.
3. No painel de ações, clique **em Adicionar Token-Signing Certificate**.
4. Selecione o novo certificado na lista de certificados apresentados e, em seguida, clique em OK.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Atualizar A AD AD com o novo certificado de assinatura de token
Abra o Módulo de Diretório Ativo Microsoft Azure para Windows PowerShell. Em alternativa, abra o Windows PowerShell e, em seguida, executar o comando `Import-Module msonline`

Ligue-se ao Azure AD execute o seguinte comando: `Connect-MsolService` e, em seguida, insira as suas credenciais de administrador global.

>[!Note]
> Se estiver a executar estes comandos num computador que não seja o servidor da federação primária, insira primeiro o seguinte comando: `Set-MsolADFSContext –Computer <servername>` . <servername>Substitua-o pelo nome do servidor AD FS. Em seguida, insira as credenciais de administrador para o servidor AD FS quando solicitado.

Opcionalmente, verifique se é necessária uma atualização verificando as informações do certificado em Azure AD. Para tal, executar o seguinte comando: `Get-MsolFederationProperty` . Insira o nome do domínio federado quando solicitado.

Para atualizar as informações do certificado em Azure AD, executar o seguinte comando: `Update-MsolFederatedDomain` e, em seguida, inserir o nome de domínio quando solicitado.

>[!Note]
> Se vir um erro ao executar este comando, executa o seguinte comando: `Update-MsolFederatedDomain –SupportMultipleDomain` e, em seguida, introduza o nome de domínio quando solicitado.

## <a name="replace-ssl-certificates"></a>Substituir certificados SSL
No caso de precisar de substituir o seu certificado de assinatura simbólica por causa de um compromisso, deverá também revogar e substituir os certificados SSL para AD FS e os seus servidores WAP.  

A revogação dos seus certificados SSL deve ser feita na autoridade de certificados (CA) que emitiu o certificado.  Estes certificados são frequentemente emitidos por fornecedores de terceiros, como o GoDaddy.  Por exemplo, consulte (revogar um certificado | Certificados SSL - GoDaddy Help US).  Para mais informações consulte [como funciona a revogação do certificado.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN)

Uma vez revogado o antigo certificado SSL e emitido um novo, pode substituir os certificados SSL. Para obter mais informações consulte [substituir o certificado SSL por AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).


## <a name="remove-your-old-certificates"></a>Remova os seus certificados antigos
Depois de ter substituído os certificados antigos, deverá retirar o certificado antigo porque ainda pode ser utilizado. . Para isso, siga os passos abaixo:.  Para isso, siga os passos abaixo:

1. Certifique-se de que está ligado ao servidor FS AD primário.
2. Abra o Windows PowerShell Como um administrador. 
4. Para remover o antigo certificado de assinatura de fichas: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` .

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Atualizar parceiros da federação que podem consumir metadados da Federação
Se renovou e configurar um novo certificado de assinatura simbólica ou de desencriptação simbólica, deve certificar-se de que todos os seus parceiros da federação (parceiros de organização de recursos ou organizações de conta que estão representados no seu FS AD, confiando em fundos de reivindicações e fidedignidades de sinistros) tenham recolhido os novos certificados.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Atualizar parceiros da federação que NÃO podem consumir Metadados da Federação
Se os seus parceiros da federação não puderem consumir os seus metadados da federação, deve enviar-lhes manualmente a chave pública do seu novo certificado de assinatura/desencriptação token. Envie a sua nova chave pública de certificado (.cer ficheiro ou .p7b se quiser incluir toda a cadeia) para todos os seus parceiros de organização de recursos ou organizações de conta (representados no seu FS AD, confiando em fundos partidários e fidedignidades do fornecedor de sinistros). Os parceiros implementem alterações do seu lado para confiarem nos novos certificados.



## <a name="revoke-refresh-tokens-via-powershell"></a>Revogue tokens de renovação via PowerShell
Agora queremos revogar fichas de atualização para os utilizadores que os possam ter e forçá-los a re-inícion e obter novos tokens.  Isto irá tirar os utilizadores do seu telemóvel, sessões de webmail atuais, juntamente com outros itens que estão a usar Tokens e Refresh Tokens.  As informações podem ser [encontradas aqui](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) e também pode referir como revogar o acesso do [utilizador no Azure Ative Directory](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Passos seguintes

- [Gestão de Certificados SSL em AD FS e WAP no Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Obter e Configurar Certificados de Assinatura token e de desencriptação token para FS AD](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Renovar certificados da federação para Microsoft 365 e Azure Ative Directory](how-to-connect-fed-o365-certs.md)



















