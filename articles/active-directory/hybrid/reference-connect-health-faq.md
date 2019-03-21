---
title: Azure Active Directory Connect FAQ de estado de funcionamento - Azure | Documentos da Microsoft
description: Encontre respostas para perguntas sobre o Azure AD Connect Health. Estas perguntas mais frequentes incluem perguntas sobre como utilizar o serviço, incluindo o modelo de faturação, capacidades, limitações e suporte.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec88caafa9a6168860a8e9e2ff9e2abe0cfd0e77
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852982"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Perguntas mais frequentes do Azure AD Connect Health
Este artigo contém respostas para perguntas mais frequentes (FAQ) sobre o Azure Active Directory (Azure AD) Connect Health. Estas FAQ sobre perguntas sobre como utilizar o serviço, que inclui a faturação modelo, capacidades, limitações e suporte.

## <a name="general-questions"></a>Perguntas gerais
**P: Posso gerir vários diretórios do Azure AD. Como posso alternar para aquele que tem o Azure Active Directory Premium?**

Para alternar entre diferentes inquilinos do Azure AD, selecione o atualmente com sessão iniciada **nome de utilizador** no canto superior direito canto e, em seguida, selecione a conta apropriada. Se a conta não estiver listada aqui, selecione **terminar sessão**e, em seguida, utilizar as credenciais de administrador global do diretório que tem o Azure Active Directory Premium ativado para iniciar sessão.

**P: Qual a versão das funções de identidade são suportadas pelo Azure AD Connect Health?**

A tabela seguinte lista as funções e as versões de sistema operativo suportadas.

|Função| Sistema operativo / versão|
|--|--|
|Serviços de Federação do Active Directory (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Versão 1.0.9125 ou superior|
|Serviços de domínio do Active Directory (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Tenha em atenção que as funcionalidades fornecidas pelo serviço podem divergir com base na função e o sistema operativo. Em outras palavras, todas as funcionalidades poderão não estar disponíveis para todas as versões de sistema operativo. Consulte as descrições de funcionalidade para obter detalhes.

**P: O número de licenças é necessário monitorizar a minha infraestrutura?**

* O primeiro agente de estado de funcionamento do Connect requer pelo menos uma licença do Azure AD Premium.
* Cada agente registado adicional requer 25 licenças adicionais do Azure AD Premium.
* Contagem de agentes é equivalente ao número total de agentes que estão registados em todas as funções monitorizadas (AD FS, Azure AD Connect, e/ou AD DS).
* Licenciamento do AAD Connect Health não necessita de atribuir a licença aos utilizadores específicos. Só tem de ter o número previsto de licenças válidas.

Informações de licenciamento também pode ser encontradas no [página de preços do Azure AD](https://aka.ms/aadpricing).

Exemplo:

| Agentes registrados | Licenças necessárias | Configuração de monitorização de exemplo |
| ------ | --------------- | --- |
| 1 | 1 | 1 servidor do azure AD Connect |
| 2 | 26| 1 servidor do azure AD Connect e o controlador de 1 domínio |
| 3 | 51 | 1 servidor de serviços de Federação do Active Directory (AD FS), o proxy de 1 AD FS e o controlador de 1 domínio |
| 4 | 76 | Servidor 1 AD FS, proxy 1 AD FS e 2 controladores de domínio |
| 5 | 101 | 1 servidor do azure AD Connect, servidor 1 AD FS, proxy 1 AD FS e 2 controladores de domínio |

**P: O Azure AD Connect Health oferece suporte do Azure Germany Cloud?**

O Azure AD Connect Health não é suportado na Cloud da Alemanha, exceto para o [funcionalidade de relatório de erros de sincronização](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Funções | Funcionalidades | Suportado na Cloud da Alemanha |
| ------ | --------------- | --- |
| Connect Health para sincronização | Monitorização / Insight / alertas / análise | Não |
|  | Relatório de erros de sincronização | Sim |
| Connect Health para AD FS | Monitorização / Insight / alertas / análise | Não |
| Connect Health para o ADDS | Monitorização / Insight / alertas / análise | Não |

Para garantir a conectividade de agente do Connect Health para sincronização, configure as [requisito de instalação](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) em conformidade.

## <a name="installation-questions"></a>Perguntas de instalação

**P: O que é o impacto da instalação do agente do Azure AD Connect estado de funcionamento em servidores individuais?**

O impacto da instalação do agente do Microsoft Azure AD Connect Health, do AD FS, servidores de proxy de aplicações web, servidores do Azure AD Connect (sincronização), controladores de domínio é mínimo em relação à CPU, consumo de memória, largura de banda de rede e armazenamento.

Os números a seguir são uma aproximação:

* Consumo da CPU: ~ 1 a 5% de aumento.
* Consumo de memória: Até 10% da memória total do sistema.

> [!NOTE]
> Se o agente não consegue comunicar com o Azure, o agente armazena os dados localmente para um limite máximo definido. O agente substitui os dados "em cache" numa base "menos recentemente servidos".
>
>

* Armazenamento de memória intermédia local para agentes do Azure AD Connect Health: ~ 20 MB.
* Para servidores do AD FS, recomendamos que aprovisiona um espaço de disco de 1.024 MB (1 GB) para o canal de auditoria do AD FS para o Azure agentes AD Connect Health processar todos os dados de auditoria antes que ela seja substituída.

**P: Será necessário reiniciar os meus servidores durante a instalação de agentes do Azure AD Connect Health?**

Não. A instalação dos agentes não exigirá que reiniciar o servidor. No entanto, a instalação de alguns passos de pré-requisitos pode exigir um reinício do servidor.

Por exemplo, no Windows Server 2008 R2, a instalação do .NET 4.5 Framework requer a reinicialização do servidor.

**P: O Azure AD Connect Health funciona através de um proxy HTTP pass-through?**

Sim. Para operações em curso, pode configurar o agente de estado de funcionamento para utilizar um proxy HTTP para reencaminhar pedidos de HTTP de saída.
Leia mais sobre [configurar o Proxy de HTTP para os agentes de estado de funcionamento](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Se precisar de configurar um proxy durante o registo do agente, poderá ter de modificar as definições de Proxy do Internet Explorer com antecedência.

1. Abra o Internet Explorer > **configurações** > **opções da Internet** > **ligações** > **definições de LAN** .
2. Selecione **utilizar um servidor Proxy para a rede local**.
3. Selecione **avançadas** se tiver portas de proxy diferente para HTTP e HTTPS/seguro.

**P: O Azure AD Connect Health suporta autenticação básica quando se liga ao HTTP proxies?**

Não. Um mecanismo para especificar um nome de usuário arbitrários e a palavra-passe para a autenticação básica não é atualmente suportado.

**P: Que portas de firewall é necessário abrir para o agente do Azure AD Connect estado de funcionamento trabalhar?**

Consulte a [secção de requisitos](how-to-connect-health-agent-install.md#requirements) para obter a lista de portas de firewall e outros requisitos de conectividade.

**P: Por que vejo os dois servidores com o mesmo nome no portal do Azure AD Connect Health?**

Quando remove um agente de um servidor, o servidor não é removido automaticamente do portal do Azure AD Connect Health. Se remover um agente de um servidor manualmente ou remova o servidor propriamente dito, terá de eliminar manualmente a entrada do servidor a partir do portal do Azure AD Connect Health.

Pode recriar a imagem de um servidor ou criar um novo servidor com os detalhes do mesmo (por exemplo, o nome do computador). Se não tenha removido o servidor já registado a partir do portal do Azure AD Connect Health e tiver instalado o agente no novo servidor, poderá ver duas entradas com o mesmo nome.

Neste caso, elimine manualmente a entrada que pertence o servidor antigo. Os dados para este servidor devem estar desatualizados.

## <a name="health-agent-registration-and-data-freshness"></a>Atualização de dados e de registo do agente de estado de funcionamento

**P: Quais são as razões comuns para as falhas de registo do agente de estado de funcionamento e como resolver problemas?**

O agente de estado de funcionamento poderão não conseguir registar devido a algumas das razões possíveis seguintes:

* O agente não consegue comunicar com os pontos finais necessários, porque existe uma firewall a bloquear tráfego. Isso é particularmente comum em servidores de proxy de aplicações web. Certifique-se de que tem a permissão comunicações de saída para as portas e pontos finais necessários. Consulte a [secção de requisitos](how-to-connect-health-agent-install.md#requirements) para obter detalhes.
* Comunicações de saída estão sujeita à inspeção do SSL pela camada de rede. Isso faz com que o certificado que o agente utiliza para ser substituído pela inspeção servidor/entidade e os passos para concluir o registo do agente falharem.
* O utilizador não tem acesso para efetuar o registo do agente. Por predefinição, os administradores globais têm acesso. Pode usar [controlo de acesso baseado em funções](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) para delegar o acesso a outros utilizadores.

**P: Eu estou obtendo alertado que "dados do serviço de estado de funcionamento não estão atualizados." Como posso resolver o problema?**

O Azure AD Connect Health gera o alerta quando não recebe todos os pontos de dados do servidor nas últimas duas horas. [Leia mais](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Perguntas de operações
**P: É necessário ativar a auditoria em servidores de proxy de aplicações web?**

Não, a auditoria não precisa de ser ativada em servidores de proxy de aplicações web.

**P: Como são resolvidas alertas do Azure AD Connect Health?**

Alertas do Azure AD Connect Health são resolvidas numa condição de sucesso. Agentes do Azure AD Connect Health detetar e comunicar as condições de êxito ao serviço periodicamente. Para alguns alertas, a supressão é baseado no tempo. Em outras palavras, se a mesma condição de erro não é observada dentro de 72 horas a partir de geração de alertas, o alerta é resolvido automaticamente.

**P: Eu estou obtendo alertado que "o pedido de autenticação de teste (transação sintética) não conseguiu obter um token." Como posso resolver o problema?**

O Azure AD Connect Health para AD FS gera este alerta quando o agente de estado de funcionamento instalado num servidor do AD FS não consegue obter um token como parte de uma transação sintética iniciada pelo agente de estado de funcionamento. O agente de estado de funcionamento utiliza o contexto de sistema local e tenta obter um token para uma reposição personalizada da entidade confiadora de terceiros. Este é um teste de catch-all para se certificar de que o AD FS está num Estado de emissão de tokens.

Frequentemente este teste falha porque o agente de estado de funcionamento não consegue resolver o nome do farm do AD FS. Isto pode acontecer se os servidores AD FS estão por trás de um balanceadores de carga de rede e o pedido obtém iniciado a partir de um nó que está por detrás do Balanceador de carga (ao contrário de um cliente regular que está à frente do Balanceador de carga). Isso pode ser corrigido ao atualizar o ficheiro "hosts" localizado em "C:\Windows\System32\drivers\etc" para incluir o endereço IP do servidor do AD FS ou um endereço IP de loopback (127.0.0.1) para o nome do farm do AD FS (por exemplo, sts.contoso.com). Adicionar o ficheiro do anfitrião serão cortar a chamada de rede, permitindo assim que o agente de estado de funcionamento obter o token.

**P: Recebi uma mensagem de e-mail com a indicação de que minhas máquinas não sejam corrigidas para os recentes ataques de ransomware. Por que recebeu este e-mail?**

O serviço do Azure AD Connect Health analisados todas as máquinas que monitoriza para garantir que os patches necessários foram instaladas. O e-mail foi enviado para os administradores do inquilino se pelo menos uma máquina não tinha os patches críticos. A seguinte lógica foi utilizada para efetuar este determinação.
1. Encontre todos os hotfixes instalados na máquina.
2. Verifique se pelo menos uma das correções da lista definida está presente.
3. Se Sim, a máquina está protegida. Caso contrário, a máquina está em risco para o ataque.

Pode utilizar o seguinte script do PowerShell para executar esta verificação manualmente. Ele implementa a lógica acima.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**P: Por que faz o cmdlet do PowerShell <i>Get-MsolDirSyncProvisioningError</i> mostrar menos erros de sincronização no resultado?**

<i>Get-MsolDirSyncProvisioningError</i> só irá devolver erros de aprovisionamento do DirSync. Além disso, portal do Connect Health também mostra outra sincronização de tipos de erro, como erros de exportação. Isso é consistente com o resultado de delta do Azure AD Connect. Leia mais sobre [erros do Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**P: Por que razão as minhas ADFS Audita a ser gerado, não?**

Utilize o cmdlet do PowerShell <i>Get-AdfsProperties - AuditLevel</i> garantir que os registos de auditoria não está no estado desativado. Leia mais sobre [registos de auditoria do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Observe que se são avançadas de definições de auditoria enviadas para o servidor do AD FS, quaisquer alterações Auditpol.exe serão substituído (evento se a aplicação gerada não está configurada). Neste caso, defina a política de segurança local para iniciar sessão falhas de aplicação gerados e o sucesso.

**P: Quando o certificado do agente serão renovada antes de expiração automática?**
A certificação do agente será automática renovado **6 meses** antes da data de expiração. Se não for renovado, certifique-se que a ligação de rede do agente está estável. Reinicie os serviços do agente ou atualização para a versão mais recente também poderá resolver o problema.


## <a name="related-links"></a>Ligações relacionadas
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operações do Azure AD Connect Health](how-to-connect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [Histórico de versões do Azure AD Connect Health](reference-connect-health-version-history.md)
