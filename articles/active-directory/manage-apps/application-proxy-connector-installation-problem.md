---
title: Problema ao instalar o Conector do Agente do Proxy de Aplicações
description: Como resolver problemas que poderá enfrentar ao instalar o Conector de Agente Proxy de Aplicação para o Diretório Ativo Azure.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0f76f03883746b6f4b87bb817f8adde850ed28b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253669"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problema ao instalar o Conector do Agente do Proxy de Aplicações

Microsoft Azure Ative Directory Application Proxy Connector é um componente de domínio interno que utiliza ligações de saída para estabelecer a conectividade do ponto final disponível na nuvem para o domínio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Áreas de problemas gerais com instalação do conector

Quando a instalação de um conector falha, a causa raiz é geralmente uma das seguintes áreas. **Como precursor de qualquer resolução de problemas, certifique-se de reiniciar o conector.**

1.  **Conectividade** – para concluir uma instalação bem sucedida, o novo conector precisa de registar e estabelecer futuras propriedades fidediárias. Isto é feito ligando-se ao serviço de cloud Proxy da Aplicação Proxy do Azure Ative.

2.  **Trust Establishment** – o novo conector cria um certificado auto-assinado e regista-se no serviço de nuvem.

3.  **Autenticação do administrador** – durante a instalação, o utilizador deve fornecer credenciais de administração para completar a instalação do Conector.

> [!NOTE]
> Os registos de instalação do Conector podem ser encontrados na pasta %TEMP% e podem ajudar a fornecer informações adicionais sobre o que está a causar uma falha de instalação.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verifique a conectividade com o serviço Proxy da Aplicação cloud e a página de Login do Microsoft

**Objetivo:** Verifique se a máquina de conector pode ligar-se ao ponto de partida de registo do Application Proxy, bem como à página de login da Microsoft.

1.  No servidor do conector, esgoe um teste de porta utilizando [telnet](/windows-server/administration/windows-commands/telnet) ou outra ferramenta de teste de porta para verificar se as portas 443 e 80 estão abertas.

2.  Se alguma dessas portas não for bem sucedida, verifique se o Firewall ou o representante de backend têm acesso aos domínios e portas necessários, [prepare o ambiente no local](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Abra um browser (separador separado) e vá para a seguinte página web: `https://login.microsoftonline.com` , certifique-se de que pode iniciar sessão nessa página.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Verifique o suporte de componentes de máquina e backend para certificado de confiança de procuração de aplicação

**Objetivo:** Verifique se a máquina de conector, o proxy e a firewall podem suportar o certificado criado pelo conector para futura confiança e se o certificado é válido.

>[!NOTE]
>O conector tenta criar um certificado SHA512 que é suportado por TLS1.2. Se a máquina ou a firewall de backend e o proxy não suportam TLS1.2, a instalação falha.
>
>

**Rever os requisitos necessários:**

1.  Verifique se a máquina suporta TLS1.2 – Todas as versões do Windows depois de 2012 R2 devem suportar TLS 1.2. Se a sua máquina de conector for de uma versão de 2012 R2 ou anterior, certifique-se de que os seguintes KBs estão instalados na máquina: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Contacte a sua administração de rede e peça para verificar se o representante e firewall de backend não bloqueiam o TRÁFEGO SHA512 para o tráfego de saída.

**Para verificar o certificado do cliente:**

Verifique a impressão digital do certificado de cliente atual. A loja de certificados pode ser encontrada em `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` .

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Os possíveis **valores isInUserStore** são **verdadeiros** e **falsos.** Um valor **verdadeiro** significa que o certificado renovado automaticamente é armazenado no contentor pessoal na loja de certificados de utilizador do Serviço de Rede. Um valor **de falso** significa que o certificado de cliente foi criado durante a instalação ou registo iniciado pelo comando Register-AppProxyConnector e está armazenado no contentor pessoal na loja de certificados da máquina local.

Se o valor for **verdadeiro,** siga estes passos para verificar o certificado:
1. Baixar [PsTools.zip](/sysinternals/downloads/pstools)
2. Extrair [o PsExec](/sysinternals/downloads/psexec) do pacote e executar **o psexec -i-u "nt authority\network service" cmd.exe** de uma solicitação de comando elevada.
3. Executar **certmgr.msc** no pedido de comando recém-aparecido
4. Na consola de gestão expanda o recipiente Pessoal e clique em Certificados
5. Localize o certificado emitido por **connectorregistrationca.msappproxy.net**

Se o valor for **falso,** siga estes passos para verificar o certificado:
1. Executar **certlm.msc**
2. Na consola de gestão expanda o recipiente Pessoal e clique em Certificados
3. Localize o certificado emitido por **connectorregistrationca.msappproxy.net**

**Para renovar o certificado de cliente:**

Se um conector não estiver ligado ao serviço durante vários meses, os seus certificados podem estar desatualizados. A falta de renovação do certificado leva a um certificado caducado. Isto faz com que o serviço de conector deixe de funcionar. O evento 1000 é registado no registo administrativo do conector:

"O re-registo do conector falhou: o certificado de confiança do Conector expirou. Executar o cmdlet PowerShell Register-AppProxyConnector no computador em que o Conector está a funcionar para voltar a registar o seu Conector."

Neste caso, desinstale e reinstale o conector para acionar o registo ou pode executar os seguintes comandos PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Para saber mais sobre o comando Register-AppProxyConnector, consulte [Criar um script de instalação sem supervisão para o conector Azure AD Application Proxy](./application-proxy-register-connector-powershell.md)

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifique se a administração é utilizada para instalar o conector

**Objetivo:** Verifique se o utilizador que tenta instalar o conector é um administrador com credenciais corretas. Atualmente, o utilizador deve ser pelo menos um administrador de aplicação para que a instalação tenha sucesso.

**Para verificar as credenciais estão corretas:**

Ligue-se `https://login.microsoftonline.com` e use as mesmas credenciais. Certifique-se de que o login é bem sucedido. Pode verificar a função do utilizador indo para Utilizadores e **Grupos de Diretório Azure Ative**  - &gt;   - &gt; **Todos os Utilizadores**. 

Selecione a sua conta de utilizador e, em seguida, "Papel de Diretório" no menu resultante. Verifique se a função selecionada é "Administrador de Aplicação". Se não conseguir aceder a nenhuma das páginas ao longo destes degraus, não tem o papel necessário.

## <a name="next-steps"></a>Passos seguintes
[Compreenda os conectores Proxy de aplicação AD Azure](application-proxy-connectors.md)
