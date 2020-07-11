---
title: Como importar e exportar configurações de configuração AD Connect Azure
description: Este documento descreve perguntas frequentes para o provisionamento em nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a13236294f74bbe4bdaf8c1a30408afad09d9796
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225331"
---
# <a name="importing-and-exporting-azure-ad-connect-configuration-settings-public-preview"></a>Importar e exportar definições de configuração AZure AD Connect (Visualização pública) 

As implementações do Azure AD Connect variam de uma instalação de modo expressão de uma única floresta, até implementações complexas que sincronizam em várias florestas usando regras de sincronização personalizadas. Devido ao grande número de opções e mecanismos de configuração, é essencial entender quais as definições em vigor e ser capaz de implementar rapidamente um servidor com uma configuração idêntica. Esta funcionalidade introduz a capacidade de catalogar a configuração de um determinado servidor de sincronização e importar as definições para uma nova implementação. Diferentes configurações de sincronização podem ser comparadas para visualizar facilmente as diferenças entre dois servidores ou o mesmo servidor ao longo do tempo. 

Cada vez que a configuração é alterada a partir do assistente Azure AD Connect, um novo ficheiro de definições JSON carimbado pelo tempo é automaticamente exportado para **%ProgramData%\\AADConnect**. O nome de ficheiro de definições é do formulário **Applied-SynchronizationPolicy-*. JSON** onde a última parte do nome de arquivo é uma hora de tempo. 

>[!IMPORTANT]
> Apenas as alterações efetuadas pelo Azure AD Connect são exportadas automaticamente. Quaisquer alterações efetuadas utilizando o PowerShell, o Gestor de Serviços de Sincronização ou o Editor de Regras de Sincronização, devem ser exportadas a pedido, conforme necessário para manter uma cópia atualizada. As exportações a pedido também podem ser utilizadas para colocar uma cópia das definições num local seguro para fins de recuperação de catástrofes. 

## <a name="exporting-azure-ad-connect-settings"></a>Exportação de definições de conexão Ad AD Azure 

Para visualizar um resumo das definições de configuração, abra a ferramenta Azure AD Connect e escolha a tarefa adicional denominada: Visualizar ou exportar configuração de corrente. Um resumo rápido das suas definições é mostrado juntamente com a capacidade de exportar a configuração completa do seu servidor. 

Por predefinição, as definições serão exportadas para **%ProgramData%\AADConnect,** no entanto poderá optar por guardar as definições para um local protegido para garantir a disponibilidade em caso de catástrofe. As definições são exportadas utilizando o formato de ficheiro JSON e não devem ser criadas à mão ou editadas para garantir uma consistência lógica. Importar ficheiros criados ou editados não é suportado e pode levar a resultados inesperados. 

## <a name="importing-azure-ad-connect-settings"></a>Definições de conexão Azure AD importando 

Para importar configurações previamente exportadas, faça o seguinte:
 
1. instalar **O AZURE AD Connect** num novo servidor. 
2. **Selecione Personalizar** a opção após a página **Welcome.** 
3. Clique **nas definições de sincronização de importação**. Navegue para o ficheiro de definições json previamente exportado.  
4. Clique em **Install** (Instalar).

![Instalar componentes](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Por favor, substitua as definições nesta página, como a utilização do SQL Server em vez do LocalDB ou a utilização da conta de serviço existente em vez de VSA padrão, etc. Estas definições não são importadas a partir do ficheiro de definições de configuração, mas estão lá para fins de informação e comparação.

### <a name="import-installation-experience"></a>Experiência de instalação de importação 

A experiência de instalação de importação é mantida intencionalmente simples com entradas mínimas do utilizador para facilitar a reprodutibilidade de um servidor existente.  

Estas são as únicas alterações que podem ser feitas durante a experiência de instalação. Todas as outras alterações podem ser efetuadas após a instalação do assistente Azure AD Connect.: 
- Credenciais de **Diretório Ativo Azure**   – o nome da conta do Administrador Global Azure utilizado para configurar o servidor original é sugerido por padrão, e **DEVE**   ser alterado se desejar sincronizar informações a um novo diretório.
- **Inscrição do**   utilizador – as opções de inscrição configuradas para o seu servidor original são selecionadas por predefinição e solicitarão automaticamente credenciais ou outras informações necessárias durante a configuração. Em casos raros, pode haver a necessidade de configurar um servidor com diferentes opções para evitar alterar o comportamento do servidor ativo. Caso contrário, basta premir Seguinte para utilizar as mesmas definições. 
- Credenciais de **Diretório no local**   – para cada diretório no local incluído nas definições de sincronização, deve fornecer credenciais para criar uma conta de sincronização ou fornecer uma conta de sincronização personalizada pré-criada. Este procedimento é idêntico à experiência de instalação limpa com a exceção de que não é possível adicionar ou remover diretórios. 
- Opções de **configuração**   – tal como acontece com uma instalação limpa, pode optar por configurar as definições iniciais para iniciar a sincronização automática ou ativar o modo de paragem. A principal diferença é que o Modo de Preparação é intencionalmente ativado por padrão para permitir a comparação dos resultados de configuração e sincronização antes de exportar ativamente os resultados para Azure. 

![Ligar diretórios](media/how-to-connect-import-export-config/import2.png)

>[!NOTE]
>Apenas um servidor de sincronização pode estar no papel principal e exportar ativamente alterações de configuração para Azure. Todos os outros servidores devem ser colocados no modo de preparação. 

## <a name="migrating-settings-from-an-existing-server"></a>Definições migratórias de um servidor existente 

Se um servidor existente não suportar a gestão de definições, pode optar por atualizar o servidor no local ou migrar as definições para utilização num novo servidor de paragem.  

A migração requer a execução de um script PowerShell que extrai as definições existentes para utilização numa nova instalação.Este método é recomendado para catalogar as definições do seu servidor existente e, em seguida, aplicá-las a um corte de encenação recentemente instalado.Comparar as definições do servidor original com o servidor recém-criado visualizará rapidamente as alterações entre os servidores.Como sempre, siga o processo de certificação da sua organização para garantir que não é necessária nenhuma configuração adicional.  

### <a name="migration-process"></a>Processo de Migração 
Para migrar as definições, faça o seguinte:

1. Abra o CMD como administrador no novo servidor de encenação.
2. Extrair **AzureADConnect.msi** executando o seguinte: `msiexec /a msifile/qb TARGETDIR=targetpath` onde o **msifile** é o endereço do msi e o **caminho-alvo** é o diretório para o qual pretende extrair os ficheiros.
   
   Exemplo:`msiexec /a "C:\Holding\AzureADConnect.msi" TARGETDIR="C:\extractedfiles"`
3. Copie **MigrateSettings.ps1** do diretório microsoft Ad Connect\Tools para uma localização no servidor existente.  Por exemplo, C:\configuração.  Onde a configuração é um diretório que foi criado no servidor existente. 
![Ligar diretórios](media/how-to-connect-import-export-config/migrate1.png)

4. Execute o script como mostrado abaixo e guarde todo o diretório de configuração do servidor de nível inferior. Copie este diretório para o novo servidor de encenação. Por favor, note que precisa copiar toda a pasta **Exported-ServerConfiguration-*** para o novo servidor. 
 ![Ligar diretórios](media/how-to-connect-import-export-config/migrate2.png)

 ![Ligar diretórios](media/how-to-connect-import-export-config/migrate3.png)

5. Lance **Azure AD Connect** clicando duas vezes no ícone no ambiente de trabalho. Aceite o EULA, na página seguinte clique no botão **Personalizar.** 
6. Selecione A caixa de **verificação de sincronização de importação** e clique no botão **Procurar** para navegar na pasta Exported-ServerConfiguration-* e selecione a MigratedPolicy.jspara importar as definições migradas.
 ![Ligar diretórios](media/how-to-connect-import-export-config/migrate4.png)

7. Para comparar as definições migradas com as das definições aplicadas, procure a mais recente **Migração-SincronizaçãoPolítica-*. JSON** e **Applied-SynchronizationPolicy-*. JSON** (* é a data de marcação) em **%ProgramData%\AADConnect**. Use a sua ferramenta de comparação de ficheiros favorita para comparar a paridade. 

## <a name="post-installation-verification"></a>Verificação de Instalação pós-instalação 

Comparar o ficheiro de definições originalmente importado, com o ficheiro de definições exportadas, do servidor recém-implantado é um passo essencial para compreender quaisquer diferenças entre o pretendido, contra a implantação resultante. A utilização da sua aplicação de comparação de textos lado a lado favorita produz uma visualização instantânea que realça rapidamente quaisquer alterações desejadas ou acidentais. Embora muitos passos de configuração manual anteriormente sejam eliminados, você ainda deve seguir o processo de certificação da sua organização para garantir que não é necessária configuração adicional. Esta configuração pode ocorrer se alavancar definições avançadas, que não são atualmente capturadas na versão pública da gestão de definições. 

As limitações conhecidas incluem: 
- Regras de **sincronização**   – a precedência de uma regra personalizada deve estar na gama reservada de 0-99 para evitar conflitos com as regras padrão da Microsoft. A colocação de uma regra personalizada fora do alcance reservado pode resultar na mudança da sua regra personalizada à medida que as regras padrão são adicionadas à configuração. Um problema semelhante ocorrerá se a sua configuração contiver regras padrão modificadas. Modificar uma regra padrão é fortemente desencorajado e a colocação de regras é suscetível de ser incorreta. Writeback do dispositivo – estas definições são catalogadas no entanto não são aplicadas atualmente durante a configuração. Se o registo do dispositivo tiver sido ativado para o seu servidor original, deve configurar manualmente a funcionalidade no servidor recém-implantado. 
- **Tipos de objeto**   sincronizados – embora seja possível restringir a lista de tipos de objetos sincronizados (utilizadores, contactos, grupos, etc.) utilizando o Gestor de Serviços de Sincronização, esta funcionalidade não é suportada através de definições de sincronização. Após a conclusão da instalação, deve reaplicar manualmente a configuração avançada. 
- **Perfis de execução personalizados**   - embora seja possível modificar o conjunto predefinido de perfis de execução utilizando o Gestor de Serviço de Sincronização, esta funcionalidade não é suportada atualmente através de definições de sincronização. Após a conclusão da instalação, deve reaplicar manualmente a configuração avançada. 
- **Configurar a Hierarquia**   de Provisionamento – esta característica avançada do Gestor de Serviços de Sincronização não é suportada através de definições de sincronização e deve ser reconfigurada manualmente após a conclusão da implementação inicial. 
- Autenticação AD **FS e PingFederate**   – os métodos de entrada associados a estas características de autenticação serão automaticamente pré-selecionados, no entanto deve fornecer interativamente todos os outros parâmetros de configuração necessários. 

 ## <a name="next-steps"></a>Passos Seguintes

- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições expressas](how-to-connect-install-express.md)
- [Definições personalizadas](how-to-connect-install-custom.md)
- [Instalar os agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md) 
