---
title: Começar Azure MFA Server - Azure Ative Directory
description: Introdução passo-a-passo com o Servidor MFA do Azure local
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbe8b4176e33ff04ff8033af3b50cbdd0d882ae2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919578"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Introdução ao Servidor Multi-Factor Authentication do Azure

<center>

![Começar com o MFA Server no local](./media/howto-mfaserver-deploy/server2.png)</center>

Esta página abrange uma nova instalação do servidor e a configuração da mesma com o Active Directory no local. Se já tiver o servidor MFA instalado e se pretender atualizar, veja [Atualizar para o servidor Multi-Factor Authentication mais recente do Azure](howto-mfaserver-deploy-upgrade.md). Se quiser informações sobre como instalar apenas o serviço Web, veja [Implementar o Serviço Web de Aplicações Móveis do Servidor Multi-Factor Authentication do Azure](howto-mfaserver-deploy-mobileapp.md).

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores (MFA) durante os eventos de entrada devem utilizar a autenticação multi-factor Azure baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="plan-your-deployment"></a>Planear a sua implementação

Antes de transferir o Servidor Multi-Factor Authentication do Azure, considere quais são os seus requisitos de elevada disponibilidade e carga. Utilize estas informações para decidir como e onde implementar.

Uma boa diretriz para a quantidade de memória necessária é o número de utilizadores que espera que se autentiquem regularmente.

| Utilizadores | RAM |
| ----- | --- |
| 1-10,000 | 4GB |
| 10,001-50,000 | 8 GB |
| 50,001-100,000 | 12 GB |
| 100,000-200,001 | 16 GB |
| 200,001+ | 32 GB |

Precisa de configurar múltiplos servidores para elevada disponibilidade ou balanceamento de carga? Existem várias formas de efetuar esta configuração com o Servidor MFA do Azure. Quando instala o seu primeiro Servidor MFA do Azure, este torna-se o principal. Quaisquer servidores adicionais tornam-se subordinados e sincronizam automaticamente os utilizadores e a configuração com o principal. Em seguida, pode configurar um servidor primário e os restantes agem como reserva ou pode configurar o balanceamento de carga entre todos os servidores.

Quando um Servidor MFA do Azure principal fica offline, os servidores subordinados ainda podem processar os pedidos de verificação em dois passos. No entanto, não pode adicionar novos utilizadores e os já existentes não podem atualizar as respetivas definições até que o principal esteja novamente online ou seja promovido um subordinado.

### <a name="prepare-your-environment"></a>Preparar o ambiente

Verifique se o servidor que está a utilizar para o Multi-Factor Authentication do Azure cumpre os seguintes requisitos:

| Requisitos do Servidor Multi-Factor Authentication do Azure | Descrição |
|:--- |:--- |
| Hardware |<li>200 MB de espaço no disco rígido</li><li>processador com capacidade de 32 ou 64 bits</li><li>1 GB de RAM ou superior</li> |
| Software |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008/R2 (apenas com [ESU)](https://docs.microsoft.com/lifecycle/faq/extended-security-updates)</li><li>Windows 10</li><li>Windows 8.1, todas as edições</li><li>Windows 8, todas as edições</li><li>Windows 7, todas as edições (apenas com [ESU)](https://docs.microsoft.com/lifecycle/faq/extended-security-updates)</li><li>Microsoft .NET 4.0 Framework</li><li>O IIS 7.0 ou superior, se estiver a instalar o portal de utilizador ou o SDK do serviço Web</li> |
| Permissões | Administrador de domínio ou conta de administrador empresarial para registar com Diretório Ativo |

### <a name="azure-mfa-server-components"></a>Componentes de Servidor MFA do Azure

Existem três componentes Web que compõem o servidor MFA do Azure:

* SDK do Serviço Web - permite a comunicação com os outros componentes e está instalado no servidor de aplicações MFA do Azure
* Portal de Utilizador - um site do IIS que permite aos utilizadores inscreverem-se no Multi-Factor Authentication do Azure (MFA) e fazerem a manutenção das contas deles.
* Serviço Web da Aplicação Móvel - permite utilizar uma aplicação móvel, como a aplicação Microsoft Authenticator para verificação de dois passos.

Os três componentes podem ser instalados no mesmo servidor, se o servidor tiver acesso à Internet. Se dividir os componentes, o SDK do Serviço Web é instalado no servidor de aplicações do MFA do Azure, e o Portal de Utilizador e o Serviço Web da Aplicação Móvel são instalados num servidor com acesso à Internet.

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Requisitos da firewall do Servidor Multi-Factor Authentication do Azure

Cada servidor MFA tem de conseguir comunicar na porta 443 de saída com os seguintes endereços:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Se as firewalls de saída estiverem restritas na porta 443, abra os seguintes intervalos de endereços IP:

| Subrede IP | Máscara de rede | Intervalo de IP |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254   |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

Se não estiver a utilizar a funcionalidade Confirmação de Eventos e os seus utilizadores não estiverem a utilizar aplicações móveis para verificar a partir de dispositivos na rede empresarial, só precisa dos seguintes intervalos:

| Subrede IP | Máscara de rede | Intervalo de IP |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79|
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79|
| 70.37.154.200/29 |255.255.255.248  |70.37.154.201 – 70.37.154.206  |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

## <a name="download-the-mfa-server"></a>Transferir o Servidor MFA

Siga estes passos para transferir o Servidor Multi-Factor Authentication do Azure a partir do portal do Azure:

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que gostariam de exigir a autenticação de vários fatores (MFA) dos seus utilizadores devem utilizar a autenticação multi-factor Azure baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume. Os passos seguintes só funcionam se for um cliente do MFA Server existente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Procure e selecione *Azure Active Directory*. Selecione **Security**  >  **Segurança MFA**.
3. No **Servidor MFA do Gestor**, selecione as **definições do Servidor**.
4. Selecione **Transferir** e siga as instruções na página de transferência para guardar o instalador. 

   ![Descarregue o MFA Server a partir do portal Azure](./media/howto-mfaserver-deploy/downloadportal.png)

5. Mantenha esta página aberta, uma vez que iremos referi-la depois de executar o instalador.

## <a name="install-and-configure-the-mfa-server"></a>Instalar e configurar o Servidor MFA

Agora que transferiu o servidor, pode instalá-lo e configurá-lo. Certifique-se de que o servidor no qual o está a instalar cumpre os requisitos listados na secção de planeamento.

1. Faça duplo clique no executável.
2. No ecrã selete de pasta de instalação, certifique-se de que a pasta está correta e clique em **Seguinte**.
3. Quando concluir a instalação, clique em **Concluir**. O assistente de configuração é iniciado.
4. No ecrã de boas-vindas do assistente de configuração, assinale **Não utilizar o Assistente de Configuração da Autenticação** e clique em **Seguinte**. O assistente fecha e o servidor é iniciado.

   ![Saltar usando o assistente de configuração de autenticação](./media/howto-mfaserver-deploy/skip2.png)

5. Na página a partir da qual transferiu o servidor, clique no botão **Gerar Credenciais de Ativação**. Copie estas informações para o servidor MFA do Azure nas caixas fornecidas e clique em **Ativar**.

> [!NOTE]
> Apenas os administradores globais são capazes de gerar credenciais de ativação no portal Azure.

## <a name="send-users-an-email"></a>Enviar uma mensagem de e-mail aos utilizadores

Para facilitar a implementação, permita que o Servidor MFA comunique com os seus utilizadores. O Servidor MFA pode enviar um e-mail a informá-los que foram inscritos na verificação de dois passos.

A mensagem de e-mail que envia deve ser determinada através de como configurou os seus utilizadores para a verificação de dois passos. Por exemplo, se consegue importar números de telefone do diretório da empresa, o e-mail deve incluir os números de telefone predefinidos, de modo a que os utilizadores saibam o que esperar. Se não importar os números de telefone ou se os utilizadores estiverem configurados para utilizar a aplicação móvel, envie-lhes um e-mail que os direcione para concluírem a inscrição da respetiva conta. Inclua uma hiperligação para o Portal de Utilizador da Multi-Factor Authentication do Azure na mensagem de e-mail.

O conteúdo da mensagem de e-mail também varia consoante o método de verificação definido para o utilizador (chamada telefónica, SMS ou aplicação móvel). Por exemplo, se for exigido ao utilizador que utilize um PIN quando fizer a autenticação, a mensagem de e-mail irá informá-lo de que o PIN inicial foi definido. É pedido as utilizadores que alterem o PIN durante a primeira verificação.

### <a name="configure-email-and-email-templates"></a>Configurar o e-mail e modelos de e-mail

Clique no ícone de e-mail à esquerda para configurar as definições de envio destas mensagens de e-mail. É nesta página que pode introduzir as informações de SMTP do seu servidor de correio e enviar mensagens de e-mail, assinalando a caixa de verificação **Enviar e-mails aos utilizadores**.

![Configuração do E-mail do Servidor MFA](./media/howto-mfaserver-deploy/email1.png)

No separador Conteúdo do E-mail, poderá ver os modelos de e-mail que estão disponíveis para escolha. Dependendo de como configurou os seus utilizadores para realizarem a verificação de dois passos, escolha o modelo mais adequado.

![Modelos de e-mail do MFA Server na consola](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Importar utilizadores do Active Directory

Agora que o servidor está instalado, pode adicionar utilizadores. Pode optar por criá-los manualmente, importar utilizadores do Active Directory ou configurar a sincronização automática com o Active Directory.

### <a name="manual-import-from-active-directory"></a>Importar manualmente do Active Directory

1. No Servidor MFA do Azure, à esquerda, selecione **Utilizadores**.
2. Na parte inferior, selecione **Importar do Active Directory**.
3. Agora pode pesquisar utilizadores individuais ou pesquisar UOs no diretório do AD com utilizadores nas mesmas. Neste caso, especificamos a UO dos utilizadores.
4. Realce todos os utilizadores à direita e clique em **Importar**. Deverá receber um pop-up a indicar que a importação foi bem-sucedida. Feche a janela de importação.

   ![MFA Server importa utilizadores do Ative Directory](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Sincronização automatizada com o Active Directory

1. No Servidor MFA do Azure, à esquerda, selecione **Integração do Diretório**.
2. Navegue para o separador **Sincronização**.
3. Na parte inferior, escolha **Adicionar**
4. Na caixa **Adicionar Item de Sincronização** apresentada, escolha o domínio, o UO **ou** grupo de segurança, Definições, Predefinições de Método e Predefinições de Idioma para esta tarefa de sincronização e clique em **Adicionar**.
5. Assinale a caixa com a etiqueta **Ativar sincronização com o Active Directory** e escolha um **Intervalo de sincronização** entre um minuto e 24 horas.

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Como o Servidor Multi-Factor Authentication do Azure gere dados de utilização

Quando utiliza o Servidor de Autenticação Multi-Factor (MFA) no local, os dados de um utilizador são armazenados nos servidores do local. Nenhuns dados de utilizador persistentes são armazenados na nuvem. Quando o utilizador executa uma verificação de dois passos, o Servidor MFA envia dados para o serviço cloud do MFA do Azure para executar a verificação. Quando estes pedidos de autenticação são enviados para o serviço em nuvem, os campos seguintes são enviados no pedido e nos registos para que estejam disponíveis nos relatórios de autenticação/utilização do cliente. Alguns dos campos são opcionais, pelo que podem ser ativados ou desativados no Servidor Multi-Factor Authentication. A comunicação entre o Servidor MFA e o serviço de nuvem MFA utiliza SSL/TLS através da porta 443 de saída. Estes campos são:

* ID exclusivo - nome de utilizador ou ID interno do servidor MFA
* Nome próprio e apelido (opcional)
* Endereço de e-mail (opcional)
* Número de telefone - ao realizar uma chamada de voz ou autenticação por SMS
* Token do dispositivo - ao efetuar a autenticação da aplicação móvel
* Modo de autenticação
* Resultado da autenticação
* Nome do Servidor MFA
* IP do Servidor MFA
* Cliente IP - se disponível

Além dos campos anteriores, o resultado da verificação (êxito/rejeição) e o motivo para quaisquer rejeições também são armazenados com os dados da autenticação e estão disponíveis através de relatórios de autenticação/utilização.

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamadas telefónicas não estarão disponíveis para os utilizadores do MFA Server em inquilinos AD Azure gratuitos/testados. As mensagens SMS não são afetadas por esta alteração. O telefonema continuará disponível para os utilizadores dos inquilinos pagos da AD Azure. Esta alteração só afeta inquilinos gratuitos/experimentais da Azure AD.

## <a name="back-up-and-restore-azure-mfa-server"></a>Criar cópias de segurança e restaurar o Servidor MFA do Azure

Certificar-se de que tem uma boa cópia de segurança é um passo importante a tomar em qualquer sistema.

Para fazer cópias de segurança do Servidor MFA do Azure, certifique-se de que tem uma cópia da pasta **C:\Ficheiros de Programas\Servidor Multi-Factor Authentication\Dados**, incluindo o ficheiro **PhoneFactor.pfdata**. 

Caso seja preciso um restauro, conclua os seguintes passos:

1. Reinstale o Servidor MFA do Azure num servidor novo.
2. Ative o novo Servidor MFA do Azure.
3. Pare o serviço **MultiFactorAuth**.
4. Substitua o **PhoneFactor.pfdata** pela cópia de segurança.
5. Inicie o serviço **MultiFactorAuth**.

O novo servidor está agora a funcionar com os dados de utilizador e a configuração de cópia de segurança originais.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Gerir os Protocolos TLS/SSL e Conjuntos de Cifras

Depois de ter atualizado ou instalado a versão do Servidor MFA 8.x ou superior, é recomendado desativar ou remover os conjuntos de cifras mais antigos e mais fracos, se for exigido pela sua organização. Pode encontrar informações sobre como concluir esta tarefa no artigo [Gerir Protocolos SSL/TLS e Conjuntos de Cifras para o AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

## <a name="next-steps"></a>Passos seguintes

- Defina e configure o [Portal do Utilizador](howto-mfaserver-deploy-userportal.md) para personalização por utilizadores.
- Configure e defina o Servidor MFA do Azure com o [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md), a [Autenticação RADIUS](howto-mfaserver-dir-radius.md) ou a [Autenticação LDAP](howto-mfaserver-dir-ldap.md).
- Defina e configure o [Gateway de Ambiente de Trabalho Remoto e o Servidor Multi-Factor Authentication do Azure com o RADIUS](howto-mfaserver-nps-rdg.md).
- Implementar o Serviço Web de [Aplicação Móvel do Servidor de Autenticação Multi-Factor Azure](howto-mfaserver-deploy-mobileapp.md).
- [Cenários avançados com autenticação multi-factor Azure e VPNs de terceiros](howto-mfaserver-nps-vpn.md).