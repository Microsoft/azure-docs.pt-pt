---
title: Autenticação LDAP e servidor MFA do Azure-Azure Active Directory
description: Implantando a autenticação LDAP e o Azure Servidor de Autenticação Multifator.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e24442abd707706e035bc1f9dc0a46951e0711a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848073"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Autenticação LDAP e Servidor de Autenticação Multifator do Azure

Por predefinição, o Servidor Multi-Factor Authentication do Azure está configurado para importar ou sincronizar os utilizadores do Active Directory. No entanto, pode ser configurado para ser vinculado a diferentes diretórios LDAP, como um diretório do ADAM ou controlador de domínio específico do Active Directory. Quando conectado a um diretório via LDAP, a Servidor de Autenticação Multifator do Azure pode atuar como um proxy LDAP para executar autenticações. Também permite a utilização do enlace de LDAP como destino RADIUS, para pré-autenticação de utilizadores através da utilização da Autenticação do IIS ou para autenticação primária no portal de utilizador do MFA do Azure.

Para usar a autenticação multifator do Azure como um proxy LDAP, insira o Servidor de Autenticação Multifator do Azure entre o cliente LDAP (por exemplo, dispositivo de VPN, aplicativo) e o servidor de diretório LDAP. O Servidor Multi-Factor Authentication do Azure tem de ser configurado para comunicar com os servidores cliente e o diretório LDAP. Nesta configuração, o Servidor Multi-Factor Authentication do Azure aceita os pedidos LDAP das aplicações e dos servidores cliente e reencaminha-os para o servidor de diretório LDAP de destino para validar as credenciais principais. Se o diretório LDAP validar as credenciais primárias, a autenticação multifator do Azure executará uma segunda verificação de identidade e enviará uma resposta de volta para o cliente LDAP. A autenticação completa só será bem-sucedida se tanto a autenticação do servidor LDAP, como a verificação de segundo passo, tiverem êxito.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Novos clientes que queiram exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes existentes que ativaram o servidor MFA antes de 1º de julho poderão baixar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="configure-ldap-authentication"></a>Configurar a autenticação LDAP

Para configurar a autenticação LDAP, instale o Servidor Multi-Factor Authentication do Azure num servidor do Windows. Utilize o seguinte procedimento:

### <a name="add-an-ldap-client"></a>Adicionar um cliente LDAP

1. No Servidor de Autenticação Multifator do Azure, selecione o ícone de autenticação LDAP no menu à esquerda.
2. Selecione a caixa de verificação **Ativar Autenticação LDAP**.

   ![Autenticação LDAP no servidor MFA](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. No separador Clientes, altere a porta TCP e a porta SSL se pretender vincular o serviço LDAP do Multi-Factor Authentication do Azure a portas não padrão para escutar os pedidos LDAP.
4. Se você planeja usar LDAPs do cliente para a Servidor de Autenticação Multifator do Azure, um certificado SSL deve ser instalado no mesmo servidor que o servidor MFA. Clique em **procurar** ao lado da caixa certificado SSL e selecione um certificado a ser usado para a conexão segura.
5. Clique em **Adicionar**.
6. Na caixa de diálogo Adicionar cliente LDAP, insira o endereço IP do dispositivo, servidor ou aplicativo que se autentica no servidor e um nome de aplicativo (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
7. Selecione a caixa de correspondência de utilizador **Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e forem sujeitos à verificação em dois passos. Se um número significativo de usuários ainda não tiver sido importado para o servidor e/ou estiver isento da verificação em duas etapas, deixe a caixa desmarcada. Consulte o arquivo de ajuda do servidor MFA para obter informações adicionais sobre esse recurso.

Repita estes passos para adicionar mais clientes LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Configurar a ligação do diretório LDAP

Quando o Multi-Factor Authentication do Azure está configurado para receber autenticações LDAP, é necessário utilizar o proxy nessas autenticações para o diretório LDAP. Assim, o separador Destino apenas apresenta uma única opção desativada para utilizar um destino LDAP.

> [!NOTE]
> Não há garantia de que a integração de diretórios funcione com diretórios diferentes de Active Directory Domain Services.

1. Para configurar a ligação do diretório LDAP, clique no ícone de **Integração de Diretórios**.
2. No separador Definições, selecione o botão de opção **Utilizar configuração de LDAP específica**.
3. Selecione **Editar...**
4. Na caixa de diálogo Editar Configuração de LDAP, preencha os campos com as informações necessárias para ligar ao diretório LDAP. O ficheiro de ajuda do Servidor Multi-Factor Authentication do Azure inclui descrições dos campos.

    ![Configuração LDAP de integração de diretórios](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Teste a ligação LDAP, clicando no botão **Testar**.
6. Se o teste de ligação LDAP for concluído com êxito, clique no botão **OK**.
7. Clique na guia **filtros** . O servidor está pré-configurado para carregar contêineres, grupos de segurança e usuários de Active Directory. Se vincular a outro diretório LDAP, provavelmente terá de editar os filtros apresentados. Clique na ligação **Ajuda** para obter mais informações sobre filtros.
8. Clique na guia **atributos** . O servidor está pré-configurado para mapear atributos de Active Directory.
9. Se estiver a vincular a outro diretório LDAP ou para alterar os mapeamentos de atributos pré-configurados, clique no botão **Editar...**
10. Na caixa de diálogo Editar Atributos, modifique os mapeamentos de atributos LDAP para o seu diretório. Os nomes dos atributos podem ser introduzidos ou selecionados ao clicar em **...** junto a cada campo. Clique na ligação **Ajuda** para obter mais informações sobre atributos.
11. Clique no botão **OK**.
12. Clique no ícone **Definições da Empresa** e selecione o separador **Resolução de Nomes de Utilizador**.
13. Se estiver a ligar ao Active Directory a partir de um servidor associado a um domínio, deixe o botão de opção **Utilizar identificadores de segurança (SIDs) do Windows para nomes de utilizador correspondentes** selecionado. Caso contrário, selecione o botão de opção **Utilizar o atributo de identificador exclusivo de LDAP para nomes de utilizador correspondentes**.

Se o botão de opção **Utilizar o atributo de identificador exclusivo de LDAP para nomes de utilizador correspondentes** estiver selecionado, o Servidor Multi-Factor Authentication do Azure tenta resolver cada nome de utilizador para um identificador exclusivo no diretório LDAP. Uma pesquisa LDAP é executada nos atributos de nome de usuário definidos na guia integração de diretório-> atributos. Quando um usuário é autenticado, o nome de usuário é resolvido para o identificador exclusivo no diretório LDAP. O identificador exclusivo é usado para corresponder ao usuário no arquivo de dados da autenticação multifator do Azure. Isso permite comparações que não diferenciam maiúsculas de minúsculas e formatos de nome de usuário longos e curtos.

Depois de concluir essas etapas, o servidor MFA escuta nas portas configuradas as solicitações de acesso LDAP dos clientes configurados e atua como um proxy para essas solicitações ao diretório LDAP para autenticação.

## <a name="configure-ldap-client"></a>Configurar o cliente LDAP

Para configurar o cliente LDAP, utilize as diretrizes:

* Configure o seu dispositivo, servidor ou aplicação para fazer a autenticação através de LDAP no Servidor Multi-Factor Authentication do Azure como se fosse o seu diretório LDAP. Utilize as mesmas definições que utilizaria normalmente para ligar diretamente ao seu diretório LDAP, exceto no nome do servidor ou endereço IP, que será o do Servidor Multi-Factor Authentication do Azure.
* Configure o tempo limite LDAP de 30-60 segundos para que haja tempo para validar as credenciais do usuário com o diretório LDAP, executar a verificação de segunda etapa, receber sua resposta e responder à solicitação de acesso LDAP.
* Se utilizar o LDAPS, a aplicação ou o servidor que cria as consultas LDAP tem de confiar no certificado SSL instalado no Servidor Multi-Factor Authentication do Azure.
