---
title: Autenticação LDAP e servidor do Azure MFA - o Azure Active Directory
description: A implementar a autenticação LDAP e servidor do Azure multi-factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78879f0e54dbd8f573ade7381ba83429346d00db
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314268"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Autenticação LDAP e servidor do Azure multi-factor Authentication

Por predefinição, o Servidor Multi-Factor Authentication do Azure está configurado para importar ou sincronizar os utilizadores do Active Directory. No entanto, pode ser configurado para ser vinculado a diferentes diretórios LDAP, como um diretório do ADAM ou controlador de domínio específico do Active Directory. Quando ligado a um diretório através de LDAP, o servidor do Azure multi-factor Authentication pode agir como um proxy LDAP para efetuar as autenticações. Também permite a utilização do enlace de LDAP como destino RADIUS, para pré-autenticação de utilizadores através da utilização da Autenticação do IIS ou para autenticação primária no portal de utilizador do MFA do Azure.

Para utilizar o Azure multi-factor Authentication como um proxy LDAP, insira o servidor de autenticação do multi-factor do Azure entre o cliente LDAP (por exemplo, a aplicação VPN) e o servidor de diretório LDAP. O Servidor Multi-Factor Authentication do Azure tem de ser configurado para comunicar com os servidores cliente e o diretório LDAP. Nesta configuração, o Servidor Multi-Factor Authentication do Azure aceita os pedidos LDAP das aplicações e dos servidores cliente e reencaminha-os para o servidor de diretório LDAP de destino para validar as credenciais principais. Se o diretório LDAP valida as credenciais primárias, o multi-factor Authentication do Azure realiza uma segunda verificação de identidade e envia uma resposta de volta para o cliente LDAP. A autenticação completa só será bem-sucedida se tanto a autenticação do servidor LDAP, como a verificação de segundo passo, tiverem êxito.

## <a name="configure-ldap-authentication"></a>Configurar a autenticação LDAP
Para configurar a autenticação LDAP, instale o Servidor Multi-Factor Authentication do Azure num servidor do Windows. Utilize o seguinte procedimento:

### <a name="add-an-ldap-client"></a>Adicionar um cliente LDAP

1. No servidor de autenticação do multi-factor do Azure, selecione o ícone de autenticação LDAP no menu à esquerda.
2. Selecione a caixa de verificação **Ativar Autenticação LDAP**.

   ![Autenticação LDAP](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. No separador Clientes, altere a porta TCP e a porta SSL se pretender vincular o serviço LDAP do Multi-Factor Authentication do Azure a portas não padrão para escutar os pedidos LDAP.
4. Se planear utilizar o LDAPS do cliente para o servidor do Azure multi-factor Authentication, um certificado SSL deve ser instalado no mesmo servidor como servidor de MFA. Clique em **procurar** junto ao SSL de certificado caixa e selecione um certificado a utilizar para a ligação segura.
5. Clique em **Adicionar**.
6. Na caixa de diálogo Adicionar cliente LDAP, introduza o endereço IP da aplicação ou servidor que irá autenticar no servidor e um nome de aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
7. Selecione a caixa de correspondência de utilizador **Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e forem sujeitos à verificação em dois passos. Se um número significativo de utilizadores ainda não tiverem sido importado para o servidor e/ou excluídos da verificação de dois passos, deixe a caixa desmarcada. Consulte o ficheiro para obter mais informações sobre esta funcionalidade de ajuda do servidor de MFA.

Repita estes passos para adicionar mais clientes LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Configurar a ligação do diretório LDAP

Quando o Multi-Factor Authentication do Azure está configurado para receber autenticações LDAP, é necessário utilizar o proxy nessas autenticações para o diretório LDAP. Assim, o separador Destino apenas apresenta uma única opção desativada para utilizar um destino LDAP.

1. Para configurar a ligação do diretório LDAP, clique no ícone de **Integração de Diretórios**.
2. No separador Definições, selecione o botão de opção **Utilizar configuração de LDAP específica**.
3. Selecione **Editar...**
4. Na caixa de diálogo Editar Configuração de LDAP, preencha os campos com as informações necessárias para ligar ao diretório LDAP. O ficheiro de ajuda do Servidor Multi-Factor Authentication do Azure inclui descrições dos campos.

    ![Integração de Diretórios](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Teste a ligação LDAP, clicando no botão **Testar**.
6. Se o teste de ligação LDAP for concluído com êxito, clique no botão **OK**.
7. Clique no separador **Filtros**. O Servidor está pré-configurada para carregar contentores, grupos de segurança e utilizadores do Active Directory. Se vincular a outro diretório LDAP, provavelmente terá de editar os filtros apresentados. Clique na ligação **Ajuda** para obter mais informações sobre filtros.
8. Clique no separador **Atributos**. O Servidor está pré-configurada para mapear os atributos do Active Directory.
9. Se estiver a vincular a outro diretório LDAP ou para alterar os mapeamentos de atributos pré-configurados, clique no botão **Editar...**
10. Na caixa de diálogo Editar Atributos, modifique os mapeamentos de atributos LDAP para o seu diretório. Os nomes dos atributos podem ser introduzidos ou selecionados ao clicar em **...** junto a cada campo. Clique na ligação **Ajuda** para obter mais informações sobre atributos.
11. Clique no botão **OK**.
12. Clique no ícone **Definições da Empresa** e selecione o separador **Resolução de Nomes de Utilizador**.
13. Se estiver a ligar ao Active Directory a partir de um servidor associado a um domínio, deixe o botão de opção **Utilizar identificadores de segurança (SIDs) do Windows para nomes de utilizador correspondentes** selecionado. Caso contrário, selecione o botão de opção **Utilizar o atributo de identificador exclusivo de LDAP para nomes de utilizador correspondentes**. 

Se o botão de opção **Utilizar o atributo de identificador exclusivo de LDAP para nomes de utilizador correspondentes** estiver selecionado, o Servidor Multi-Factor Authentication do Azure tenta resolver cada nome de utilizador para um identificador exclusivo no diretório LDAP. É efetuada uma pesquisa LDAP nos atributos de Nome de Utilizador definidos no separador Integração de Diretórios -> Atributos. Quando um utilizador é autenticado, o nome de utilizador é resolvido para o identificador exclusivo no diretório LDAP. O identificador exclusivo é utilizado para corresponder o utilizador no ficheiro de dados de multi-factor Authentication. Isto permite comparações de maiúsculas e minúsculas e formatos de nome de utilizador extenso e abreviado.

Depois de concluir estes passos, o servidor MFA escuta nas portas configuradas para pedidos de acesso LDAP dos clientes configurados e age como um proxy para essas solicitações para o diretório LDAP para autenticação.

## <a name="configure-ldap-client"></a>Configurar o cliente LDAP
Para configurar o cliente LDAP, utilize as diretrizes:

* Configure o seu dispositivo, servidor ou aplicação para fazer a autenticação através de LDAP no Servidor Multi-Factor Authentication do Azure como se fosse o seu diretório LDAP. Utilize as mesmas definições que utilizaria normalmente para ligar diretamente ao seu diretório LDAP, exceto no nome do servidor ou endereço IP, que será o do Servidor Multi-Factor Authentication do Azure.
* Configure o tempo limite de LDAP para 30 a 60 segundos para que haja tempo para validar as credenciais do utilizador no diretório LDAP, efetuar a verificação de segundo passo, receber a respetiva resposta e responder ao pedido de acesso LDAP.
* Se utilizar o LDAPS, a aplicação ou o servidor que cria as consultas LDAP tem de confiar no certificado SSL instalado no Servidor Multi-Factor Authentication do Azure.

