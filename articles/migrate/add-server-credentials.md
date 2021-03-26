---
title: Fornecer credenciais de servidor para descobrir inventário de software, dependências e instâncias e bases de dados do SQL Server
description: Saiba como fornecer credenciais de servidor no gestor de configuração do aparelho
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: e72d509f71704bbf8608543df5e819a9b8783935
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562074"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-and-sql-server-instances-and-databases"></a>Fornecer credenciais de servidor para descobrir inventário de software, dependências e instâncias e bases de dados do SQL Server

Siga este artigo para aprender a adicionar várias credenciais de servidor no gestor de configuração do aparelho para realizar o inventário de software (descobrir aplicações instaladas), análise de dependência de agentes e descobrir instâncias e bases de dados do SQL Server.

> [!Note]
> A descoberta e avaliação de instâncias e bases de dados do SQL Server em execução no seu ambiente VMware está agora em pré-visualização. Para experimentar esta funcionalidade, utilize [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **Leste da Austrália**. Se já tiver um projeto no Leste da Austrália e quiser experimentar esta funcionalidade, verifique se concluiu estes [**pré-requisitos**](how-to-discover-sql-existing-project.md) no portal.

O [aparelho Azure Migrate](migrate-appliance.md) é um aparelho leve utilizado pela Azure Migrate: Discovery and assessment para descobrir servidores no local em funcionamento em ambiente VMware e enviar a configuração do servidor e metadados de desempenho para o Azure. O aparelho também pode ser usado para realizar inventário de software, análise de dependência de agentes e descobrir as instâncias e bases de dados do SQL Server.

Se pretender utilizar estas funcionalidades, pode fornecer credenciais de servidor seguindo os passos abaixo. O aparelho tentará mapear automaticamente as credenciais para os servidores para executar as funcionalidades de descoberta.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Adicionar credenciais para servidores em execução em ambiente VMware

### <a name="types-of-server-credentials-supported"></a>Tipos de credenciais de servidor suportadas

Pode adicionar várias credenciais de servidor no gestor de configuração do aparelho, que podem ser credenciais de autenticação de domínio, não domínio (Windows ou Linux) ou SQL Server.

Os tipos de credenciais de servidor suportadas estão listados na tabela abaixo:

Tipo de credenciais | Description
--- | ---
**Credenciais de domínio** | Pode adicionar **credenciais de Domínio** selecionando a opção a partir do drop-down nas **credenciais Add** modal. <br/><br/> Para fornecer credenciais de domínio, é necessário especificar o **nome de Domínio** que deve ser fornecido no formato FQDN (por exemplo, prod.corp.contoso.com). <br/><br/> Também precisa de especificar um nome amigável para credenciais, nome de utilizador e senha. <br/><br/> As credenciais de domínio adicionadas serão automaticamente validadas para autenticidade contra o Diretório Ativo do domínio. Isto é para evitar qualquer bloqueio de conta quando o aparelho tenta mapear as credenciais de domínio contra servidores descobertos. <br/><br/> O aparelho não tentará mapear as credenciais de domínio que falharam na validação. É necessário ter pelo menos uma credencial de domínio validada com sucesso ou pelo menos uma credencial de não domínio para proceder ao inventário de software.<br/><br/>As credenciais de domínio mapeadas automaticamente contra os servidores do Windows serão utilizadas para executar o inventário de software e também podem ser utilizadas para descobrir casos e bases de dados do SQL Server _(se tiver configurado o modo de autenticação do Windows nos seus Servidores SQL)_.<br/> [Saiba mais](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) sobre os tipos de modos de autenticação suportados nos Servidores SQL.
**Credenciais de não domínio (Windows/Linux)** | Pode adicionar **Windows (Non-domain)** ou **Linux (Não-domínio)** selecionando a opção necessária a partir da queda nas **credenciais Add** modal. <br/><br/> Tem de especificar um nome amigável para credenciais, nome de utilizador e palavra-passe.
**Credenciais de autenticação do servidor SQL** | Pode adicionar credenciais **de autenticação do servidor SQL** selecionando a opção a partir do drop-down nas **credenciais Add** modal. <br/><br/> Tem de especificar um nome amigável para credenciais, nome de utilizador e palavra-passe. <br/><br/> Pode adicionar este tipo de credenciais para descobrir instâncias e bases de dados do SQL Server em execução no seu ambiente VMware, se tiver configurado o modo de autenticação do SQL Server nos seus Servidores SQL.<br/> [Saiba mais](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) sobre os tipos de modos de autenticação suportados nos Servidores SQL.<br/><br/> É necessário fornecer pelo menos uma credencial de domínio validada com sucesso ou pelo menos uma credencial do Windows (Não domínio) para que o aparelho possa completar o inventário de software para descobrir o SQL instalado nos servidores antes de utilizar as credenciais de autenticação do SQL Server para descobrir as instâncias e bases de dados do SQL Server.

Verifique as permissões necessárias nas credenciais Windows/Linux para realizar o inventário de software, análise de dependência de agentes e descobrir instâncias e bases de dados do SQL Server.

### <a name="required-permissions"></a>Permissões obrigatórias

A tabela abaixo lista as permissões exigidas nas credenciais do servidor fornecidas no aparelho para executar as respetivas funcionalidades:

Funcionalidade | credenciais do Windows | Credenciais linux
---| ---| ---
**Inventário de software** | Conta de utilizador convidado | Conta de utilizador regular/normal (permissões de acesso não sudo)
**Descoberta de instâncias e bases de dados do SQL Server** | Conta de utilizador que é membro da função do servidor sysadmin. | _Não suportado atualmente_
**Análise das dependências sem agente** | Conta de domínio ou não domínio (local) com permissões administrativas | Conta de utilizador raiz, ou <br/> uma conta com estas permissões em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.<br/><br/> Desardene estas capacidades utilizando os seguintes comandos: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Práticas recomendadas para fornecer credenciais

- Recomenda-se a criação de uma conta de utilizador de domínio dedicada com as [permissões necessárias,](add-server-credentials.md#required-permissions)que é procurada para realizar inventário de software, análise de dependência de agentes e descoberta de instâncias e bases de dados do SQL Server nos servidores pretendidos.
- Recomenda-se fornecer pelo menos uma credencial de domínio validada com sucesso ou pelo menos uma credencial de não domínio para iniciar o inventário de software.
- Para descobrir as instâncias e bases de dados do SQL Server, pode fornecer credenciais de domínio, se tiver configurado o modo de autenticação do Windows nos seus Servidores SQL.
- Também pode fornecer credenciais de autenticação do SQL Server se tiver configurado o modo de autenticação do SQL Server nos seus Servidores SQL, mas é aconselhável fornecer pelo menos uma credencial de domínio validada com sucesso ou pelo menos uma credencial do Windows (Não domínio) para que o aparelho possa primeiro completar o inventário.

## <a name="credentials-handling-on-appliance"></a>Manipulação de credenciais no aparelho

- Todas as credenciais fornecidas no gestor de configuração do aparelho são armazenadas localmente no servidor do aparelho e não enviadas para a Azure.
- As credenciais armazenadas no servidor do aparelho são encriptadas utilizando a API de Proteção de Dados (DPAPI).
- Depois de ter adicionado credenciais, o aparelho tenta mapear automaticamente as credenciais para realizar a descoberta nos respetivos servidores.
- O aparelho utiliza as credenciais automaticamente mapeadas num servidor para todos os ciclos de descoberta subsequentes até que as credenciais sejam capazes de obter os dados de descoberta necessários. Se as credenciais pararem de funcionar, o aparelho volta a tentar mapear a lista de credenciais adicionais e continuar a descoberta em curso no servidor.
- As credenciais de domínio adicionadas serão automaticamente validadas para autenticidade contra o Diretório Ativo do domínio. Isto é para evitar qualquer bloqueio de conta quando o aparelho tenta mapear as credenciais de domínio contra servidores descobertos. O aparelho não tentará mapear as credenciais de domínio que falharam na validação.
- Se o aparelho não conseguir mapear quaisquer credenciais de domínio ou não-domínio contra um servidor, verá o estado de "Credenciais não disponíveis" contra o servidor no seu projeto.

## <a name="next-steps"></a>Passos seguintes

Reveja os tutoriais para [a descoberta de servidores em execução no seu ambiente VMware](tutorial-discover-vmware.md)