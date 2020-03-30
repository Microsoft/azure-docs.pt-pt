---
title: Azure Ative Directory Connect Health FAQ - Azure [ Azure ] Microsoft Docs
description: Este FAQ responde a perguntas sobre a Azure AD Connect Health. Estas perguntas mais frequentes incluem perguntas sobre como utilizar o serviço, incluindo o modelo de faturação, capacidades, limitações e suporte.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0c6484f46731e0ff2d16d00cb0038202511d193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331079"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health frequentemente fez perguntas
Este artigo inclui respostas a perguntas frequentes (PERGUNTAS) sobre o Azure Ative Directory (Azure AD) Connect Health. Estas PERGUNTAS gerais cobrem questões sobre como usar o serviço, que inclui o modelo de faturação, capacidades, limitações e suporte.

## <a name="general-questions"></a>Perguntas gerais
**P: Dirijo vários diretórios da AD Azure. Como posso mudar para aquele que tem Azure Ative Directory Premium?**

Para alternar entre diferentes inquilinos da AD Azure, selecione o nome de **utilizador** atualmente assinado no canto superior direito e, em seguida, escolha a conta apropriada. Se a conta não estiver listada aqui, selecione **Sign out**, e depois use as credenciais de administração global do diretório que tem o Azure Ative Directory Premium habilitado a assinar.

**P: Que versão das funções identitárias são suportadas pela Azure AD Connect Health?**

A tabela seguinte lista as funções e as versões suportadas do sistema operativo.

|Função| Sistema operativo / Versão|
|--|--|
|Serviços de Federação do Active Directory (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Versão 1.0.9125 ou superior|
|Serviços de Domínio do Active Directory (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Note que as funcionalidades fornecidas pelo serviço podem diferir com base na função e no sistema operativo. Por outras palavras, todas as funcionalidades podem não estar disponíveis para todas as versões do sistema operativo. Consulte as descrições da funcionalidade para obter mais detalhes.

**P: Quantas licenças preciso para monitorizar a minha infraestrutura?**

* O primeiro Agente de Saúde Connect requer pelo menos uma licença Azure AD Premium.
* Cada agente registado adicional requer 25 licenças Azure AD Premium adicionais.
* A contagem de agentes equivale ao número total de agentes registados em todas as funções monitorizadas (AD FS, Azure AD Connect e/ou AD DS).
* O licenciamento DaAD Connect Health não requer que você atribua a licença a utilizadores específicos. Só precisa de ter o número necessário de licenças válidas.

As informações de licenciamento também são encontradas na página de [Preços da AD Azure](https://aka.ms/aadpricing).

Exemplo:

| Agentes registados | Licenças necessárias | Configuração de monitorização de exemplo |
| ------ | --------------- | --- |
| 1 | 1 | 1 servidor Azure AD Connect |
| 2 | 26| 1 Servidor Azure AD Connect e 1 controlador de domínio |
| 3 | 51 | 1 Servidor de Serviços da Federação de DirectórioActivo (AD FS), 1 proxy AD FS e 1 controlador de domínio |
| 4 | 76 | 1 Servidor AD FS, 1 proxy AD FS e 2 controladores de domínio |
| 5 | 101 | 1 Servidor Azure AD Connect, 1 servidor AD FS, 1 proxy AD FS e 2 controladores de domínio |

**P: Azure AD Connect Health suporta Azure Germany Cloud?**

A Azure AD Connect Health não é suportada na Cloud da Alemanha, exceto na funcionalidade de relatório de [erros de sincronização](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Funções | Funcionalidades | Apoiado na Nuvem Alemã |
| ------ | --------------- | --- |
| Ligar saúde para sincronização | Monitorização / Insight / Alertas / Análise | Não |
|  | Relatório de erro sincronizado | Sim |
| Ligue a Saúde para ADFS | Monitorização / Insight / Alertas / Análise | Não |
| Ligar a Saúde para ADDS | Monitorização / Insight / Alertas / Análise | Não |

Para garantir a conectividade do agente da Connect Health para sincronização, configure o requisito de [instalação](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) em conformidade.

## <a name="installation-questions"></a>Questões de instalação

**P: Qual é o impacto da instalação do Agente de Saúde Azure AD Connect em servidores individuais?**

O impacto da instalação do Microsoft Azure AD Connect Health Agent, AD FS, servidores proxy de aplicação web, servidores Azure AD Connect (sync), controladores de domínio é mínimo no que diz respeito ao CPU, consumo de memória, largura de banda da rede e armazenamento.

Os seguintes números são uma aproximação:

* Consumo de CpU: ~1-5% aumento.
* Consumo de memória: Até 10 % da memória total do sistema.

> [!NOTE]
> Se o agente não conseguir comunicar com o Azure, o agente armazena os dados localmente por um limite máximo definido. O agente substitui os dados "em cache" numa base "menos recentemente servida".
>
>

* Armazenamento tampão local para Agentes de Saúde Azure AD Connect: ~20 MB.
* Para servidores AD FS, recomendamos que forneça um espaço de disco de 1.024 MB (1 GB) para o canal de auditoria AD FS para os Agentes de Saúde Azure AD Connect para processar todos os dados de auditoria antes de ser substituído.

**P: Terei de reiniciar os meus servidores durante a instalação dos Agentes de Saúde Azure AD Connect?**

Não. A instalação dos agentes não exigirá que reinicie o servidor. No entanto, a instalação de alguns passos pré-requisitos pode requerer um reinício do servidor.

Por exemplo, no Windows Server 2008 R2, a instalação de .NET 4.5 Framework requer um reboot do servidor.

**P: Azure AD Connect Health funciona através de um proxy HTTP pass-through?**

Sim. Para operações em curso, pode configurar o Agente de Saúde para utilizar um representante http para reencaminhar pedidos http de saída.
Leia mais sobre [a configuração http proxy para agentes](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)de saúde .

Se precisar de configurar um proxy durante o registo do agente, poderá ter de modificar previamente as definições do Proxy do Internet Explorer.

1. Open Internet Explorer > **Configurações** > **De Internet** > **Ligações** > **LAN Definições**.
2. Selecione **Utilize um Servidor proxy para o seu LAN**.
3. Selecione **Advanced** se tiver portas de procuração diferentes para HTTP e HTTPS/Secure.

**P: A Azure AD Connect Health suporta a autenticação básica ao ligar-se aos proxies HTTP?**

Não. Não é atualmente suportado um mecanismo para especificar um nome de utilizador arbitrário e uma palavra-passe para a autenticação Básica.

**P: Que portas de firewall preciso de abrir para o Agente de Saúde Azure AD Connect funcionar?**

Consulte a [secção de requisitos](how-to-connect-health-agent-install.md#requirements) para a lista de portas de firewall e outros requisitos de conectividade.

**P: Por que vejo dois servidores com o mesmo nome no portal Azure AD Connect Health?**

Quando remove um agente de um servidor, o servidor não é automaticamente removido do portal Azure AD Connect Health. Se remover manualmente um agente de um servidor ou remover o servidor em si, tem de eliminar manualmente a entrada do servidor do portal Azure AD Connect Health.

Pode reimagem de um servidor ou criar um novo servidor com os mesmos detalhes (como o nome da máquina). Se não removeu o servidor já registado do portal Azure AD Connect Health e instalou o agente no novo servidor, poderá ver duas entradas com o mesmo nome.

Neste caso, elimine manualmente a entrada que pertence ao servidor mais antigo. Os dados deste servidor devem estar desatualizados.

## <a name="health-agent-registration-and-data-freshness"></a>Registo do Agente de Saúde e frescura de dados

**P: Quais são as razões comuns para as falhas de registo do Agente de Saúde e como posso resolver problemas?**

O agente de saúde pode não se registar devido às seguintes razões possíveis:

* O agente não pode comunicar com os pontos finais necessários porque uma firewall está bloqueando o tráfego. Isto é particularmente comum em servidores proxy de aplicação web. Certifique-se de que permitiu a comunicação de saída aos pontos finais e portas necessários. Consulte a [secção de requisitos](how-to-connect-health-agent-install.md#requirements) para mais detalhes.
* A comunicação de saída é submetida a uma inspeção tls pela camada de rede. Isto faz com que o certificado que o agente utiliza seja substituído pelo servidor/entidade de inspeção, e as etapas para completar o registo do agente falham.
* O utilizador não tem acesso para efetuar o registo do agente. Os administradores globais têm acesso por defeito. Pode utilizar o Controlo de [Acesso Baseado em Funções](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) para delegar o acesso a outros utilizadores.

**P: Estou a ser alertado que "os dados do Serviço de Saúde não estão atualizados." Como posso resolver o problema?**

A Azure AD Connect Health gera o alerta quando não recebe todos os pontos de dados do servidor nas últimas duas horas. [Leia mais](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Questões de operações
**P: Preciso de permitir a auditoria nos servidores proxy da aplicação web?**

Não, a auditoria não precisa de ser ativada nos servidores proxy da aplicação web.

**P: Como é que os Alertas de Saúde Azure AD Connect são resolvidos?**

Os alertas Azure AD Connect Health são resolvidos em condições de sucesso. Os Agentes de Saúde Azure AD Connect detetam e reportam periodicamente as condições de sucesso ao serviço. Para alguns alertas, a supressão é baseada no tempo. Por outras palavras, se a mesma condição de erro não for observada dentro de 72 horas da geração de alerta, o alerta é automaticamente resolvido.

**P: Estou a ser alertado de que "O Pedido de Autenticação de Teste (Transação Sintética) não obteve um símbolo." Como posso resolver o problema?**

A Azure AD Connect Health for AD FS gera este alerta quando o Agente de Saúde instalado num servidor AD FS não consegue obter um símbolo como parte de uma transação sintética iniciada pelo Agente de Saúde. O agente de saúde usa o contexto do sistema local e tenta obter um símbolo para uma festa auto-fivida. Este é um teste catch-all para garantir que a AD FS está em estado de emissão de fichas.

Na maioria das vezes este teste falha porque o Agente de Saúde é incapaz de resolver o nome da fazenda AD FS. Isto pode acontecer se os servidores AD FS estiverem por trás de um equilíbrio de carga de rede e o pedido for iniciado a partir de um nó que está por trás do equilibrista de carga (em oposição a um cliente regular que está na frente do equilibrador de carga). Isto pode ser corrigido atualizando o ficheiro "anfitriões" localizado em "C:\Windows\System32\drivers\etc" para incluir o endereço IP do servidor AD FS ou um endereço IP loopback (127.0.0.1) para o nome da fazenda AD FS (como sts.contoso.com). A adição do ficheiro anfitrião irá fazer um curto-circuito na chamada da rede, permitindo assim ao Agente de Saúde obter o símbolo.

**P: Recebi um e-mail indicando que as minhas máquinas NÃO estão corrigidas para os recentes ataques de ransomware. Por que recebi este e-mail?**

O serviço Azure AD Connect Health digitalizou todas as máquinas que monitoriza para garantir a instalação das correções necessárias. O e-mail foi enviado aos administradores do inquilino se pelo menos uma máquina não tivesse os patches críticos. A seguinte lógica foi usada para fazer esta determinação.
1. Encontre todos os hotfixes instalados na máquina.
2. Verifique se pelo menos um dos HotFixes da lista definida está presente.
3. Se sim, a máquina está protegida. Se não, a máquina está em risco pelo ataque.

Pode utilizar o seguinte script PowerShell para efetuar este controlo manualmente. Implementa a lógica acima.

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

**P: Porque é que o cmdlet PowerShell <i>Get-MsolDirSyncProvisioningError</i> mostra menos erros de sincronização no resultado?**

<i>O Get-MsolDirSyncProvisioningError</i> apenas devolverá erros de fornecimento do DirSync. Além disso, o portal Connect Health também mostra outros tipos de erros de sincronização, como erros de exportação. Isto é consistente com o resultado delta da Azure AD Connect. Leia mais sobre os erros de Sincronização de [Ligação AD Azure](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**P: Porque é que as minhas auditorias da ADFS não estão a ser geradas?**

Utilize powerShell cmdlet <i>Get-AdfsProperties -AuditLevel</i> para garantir que os registos de auditoria não estão em estado de desativação. Leia mais sobre [os registos de auditoria da ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Note se houver definições de auditoria avançadas empurradas para o servidor ADFS, quaisquer alterações com auditpol.exe serão substituídas (evento se a Aplicação Gerada não estiver configurada). Neste caso, por favor, estabeleça a política de segurança local para registar falhas e sucesso gerados pela Aplicação.

**P: Quando é que o certificado de agente será renovado automaticamente antes da expiração?**
A certificação do agente será renovada automaticamente **6 meses** antes da data de validade. Se não for renovado, certifique-se de que a ligação de rede do agente está estável. Reiniciar os serviços do agente ou atualizar para a versão mais recente também pode resolver o problema.


## <a name="related-links"></a>Ligações relacionadas
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalação do Agente de Saúde Azure AD Connect](how-to-connect-health-agent-install.md)
* [Operações do Azure AD Connect Health](how-to-connect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Utilização da Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [Histórico das versões do Azure AD Connect Health](reference-connect-health-version-history.md)
