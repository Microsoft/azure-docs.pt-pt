---
title: Autenticação LDAP e Servidor Azure MFA - Diretório Ativo Azure
description: Implementação de autenticação LDAP e servidor de autenticação multi-factor Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8961cccbd57294e477f0d33202fe91cd292b7814
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742226"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Autenticação LDAP e Servidor de Autenticação Multi-Factor Azure

Por predefinição, o Servidor Multi-Factor Authentication do Azure está configurado para importar ou sincronizar os utilizadores do Active Directory. No entanto, pode ser configurado para ser vinculado a diferentes diretórios LDAP, como um diretório do ADAM ou controlador de domínio específico do Active Directory. Quando ligado a um diretório via LDAP, o Azure Multi-Factor Authentication Server pode funcionar como um proxy LDAP para efetuar autenticações. Também permite a utilização do enlace de LDAP como destino RADIUS, para pré-autenticação de utilizadores através da utilização da Autenticação do IIS ou para autenticação primária no portal de utilizador do MFA do Azure.

Para utilizar a autenticação multi-factor Azure como proxy LDAP, insira o Servidor de Autenticação Multi-Factor Azure entre o cliente LDAP (por exemplo, aparelho VPN, aplicação) e o servidor de diretório LDAP. O Servidor Multi-Factor Authentication do Azure tem de ser configurado para comunicar com os servidores cliente e o diretório LDAP. Nesta configuração, o Servidor Multi-Factor Authentication do Azure aceita os pedidos LDAP das aplicações e dos servidores cliente e reencaminha-os para o servidor de diretório LDAP de destino para validar as credenciais principais. Se o diretório LDAP validar as credenciais primárias, a Autenticação Multi-Factor Azure realiza uma segunda verificação de identidade e envia uma resposta ao cliente LDAP. A autenticação completa só será bem-sucedida se tanto a autenticação do servidor LDAP, como a verificação de segundo passo, tiverem êxito.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores (MFA) durante os eventos de entrada devem utilizar a autenticação multi-factor Azure AD baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="configure-ldap-authentication"></a>Configure a autenticação LDAP

Para configurar a autenticação LDAP, instale o Servidor Multi-Factor Authentication do Azure num servidor do Windows. Utilize o seguinte procedimento:

### <a name="add-an-ldap-client"></a>Adicionar um cliente LDAP

1. No Servidor de Autenticação Multi-Factor Azure, selecione o ícone de autenticação LDAP no menu esquerdo.
2. Verifique a caixa **de verificação de autenticação ativa LDAP.**

   ![Autenticação LDAP no Servidor MFA](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. No separador Clientes, altere a porta TCP e a porta SSL (TLS) se o serviço Azure Multi-Factor Authentication LDAP se ligar a portas não standard para ouvir os pedidos de LDAP.
4. Se pretender utilizar LDAPS do cliente para o Azure Multi-Factor Authentication Server, deve ser instalado um certificado TLS/SSL no mesmo servidor que o MFA Server. Clique em navegar ao lado da caixa de **certificadoS** SSL (TLS) e selecione um certificado para utilizar para a ligação segura.
5. Clique em **Adicionar**.
6. Na caixa de diálogo do Cliente Add LDAP, insira o endereço IP do aparelho, servidor ou aplicação que autentica para o Servidor e um nome de Aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
7. Selecione a caixa de correspondência de utilizador **Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e forem sujeitos à verificação em dois passos. Se um número significativo de utilizadores ainda não tiver sido importado para o Servidor e/ou estiver isento de verificação em duas etapas, deixe a caixa descontrolada. Consulte o ficheiro de ajuda do MFA Server para obter informações adicionais sobre esta funcionalidade.

Repita estes passos para adicionar mais clientes LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Configurar a ligação do diretório LDAP

Quando o Multi-Factor Authentication do Azure está configurado para receber autenticações LDAP, é necessário utilizar o proxy nessas autenticações para o diretório LDAP. Assim, o separador Destino apenas apresenta uma única opção desativada para utilizar um destino LDAP.

> [!NOTE]
> A integração do diretório não é garantida para trabalhar com diretórios que não os Serviços de Domínio do Diretório Ativo.

1. Para configurar a ligação de diretório LDAP, clique no ícone de Integração do **Diretório.**
2. No separador Definições, selecione o botão de opção **Utilizar configuração de LDAP específica**.
3. **Selecione Editar...**
4. Na caixa de diálogo Editar Configuração de LDAP, preencha os campos com as informações necessárias para ligar ao diretório LDAP. O ficheiro de ajuda do Servidor Multi-Factor Authentication do Azure inclui descrições dos campos.

    ![Diretório integração LDAP config](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Teste a ligação LDAP, clicando no botão **Testar**.
6. Se o teste de ligação LDAP for concluído com êxito, clique no botão **OK**.
7. Clique no **separador Filtros.** O Servidor está pré-configurado para carregar contentores, grupos de segurança e utilizadores do Ative Directory. Se vincular a outro diretório LDAP, provavelmente terá de editar os filtros apresentados. Clique no link **Ajuda** para obter mais informações sobre filtros.
8. Clique no separador **Atributos.** O Servidor está pré-configurado para mapear atributos do Ative Directory.
9. Se estiver a vincular a outro diretório LDAP ou para alterar os mapeamentos de atributos pré-configurados, clique no botão **Editar...**
10. Na caixa de diálogo Editar Atributos, modifique os mapeamentos de atributos LDAP para o seu diretório. Os nomes de atributos podem ser digitado ou selecionados clicando no **...** junto a cada campo. Clique no link **Ajuda** para obter mais informações sobre atributos.
11. Clique no botão **OK.**
12. Clique no ícone **Definições da Empresa** e selecione o **separador Resolução** do nome de utilizador.
13. Se estiver a ligar ao Active Directory a partir de um servidor associado a um domínio, deixe o botão de opção **Utilizar identificadores de segurança (SIDs) do Windows para nomes de utilizador correspondentes** selecionado. Caso contrário, selecione o **atributo identificador exclusivo Use LDAP para o** botão de rádio de nomes de utilizador correspondentes.

Se o botão de opção **Utilizar o atributo de identificador exclusivo de LDAP para nomes de utilizador correspondentes** estiver selecionado, o Servidor Multi-Factor Authentication do Azure tenta resolver cada nome de utilizador para um identificador exclusivo no diretório LDAP. Uma pesquisa LDAP é realizada nos atributos username definidos no separador "Atributos > Integração do Diretório". Quando um utilizador autentica, o nome de utilizador é resolvido com o identificador único no diretório LDAP. O identificador único é utilizado para combinar o utilizador no ficheiro de dados de autenticação multi-factor Azure. Isto permite comparações insensíveis a casos e formatos de nome de utilizador longos e curtos.

Depois de completar estes passos, o MFA Server ouve nas portas configuradas para pedidos de acesso LDAP dos clientes configurados, e atua como um representante para esses pedidos para o diretório LDAP para autenticação.

## <a name="configure-ldap-client"></a>Configure o cliente LDAP

Para configurar o cliente LDAP, utilize as diretrizes:

* Configure o seu dispositivo, servidor ou aplicação para fazer a autenticação através de LDAP no Servidor Multi-Factor Authentication do Azure como se fosse o seu diretório LDAP. Utilize as mesmas definições que utilizaria normalmente para ligar diretamente ao seu diretório LDAP, exceto no nome do servidor ou endereço IP, que será o do Servidor Multi-Factor Authentication do Azure.
* Configure o tempo limite de LDAP para 30-60 segundos para que haja tempo para validar as credenciais do utilizador com o diretório LDAP, realizar a verificação em segundo passo, receber a sua resposta e responder ao pedido de acesso LDAP.
* Se utilizar LDAPS, o aparelho ou servidor que faz as consultas LDAP deve confiar no certificado TLS/SSL instalado no Servidor de Autenticação Multi-Factor Azure.
