---
title: Como usar o SDK de aplicativos móveis do Azure para Android | Microsoft Docs
description: Como usar o SDK de aplicativos móveis do Azure para Android
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b67e0eaabe63707455eaa6cd4b235ec828dddff3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025441"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Como usar o SDK de aplicativos móveis do Azure para Android

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

Este guia mostra como usar o SDK do cliente Android para aplicativos móveis para implementar cenários comuns, como:

* Consultando dados (inserindo, atualizando e excluindo).
* Authentication.
* Tratamento de erros.
* Personalizando o cliente.

Este guia se concentra no SDK do Android do lado do cliente.  Para saber mais sobre os SDKs do lado do servidor para aplicativos móveis, confira [trabalhar com o SDK de back-end do .net][10] ou [como usar o SDK de back-end do node. js][11].

## <a name="reference-documentation"></a>Documentação de Referência

Você pode encontrar a [referência de API de Javadoc][12] para a biblioteca de cliente Android no github.

## <a name="supported-platforms"></a>Plataformas com suporte

O SDK de aplicativos móveis do Azure para Android dá suporte aos níveis de API de 19 a 24 (KitKat até nougat) para fatores de forma de telefone e Tablet.  A autenticação, em particular, utiliza uma abordagem de estrutura da Web comum para coletar credenciais.  A autenticação de fluxo de servidor não funciona com dispositivos de fator forma pequeno, como inspeções.

## <a name="setup-and-prerequisites"></a>Instalação e pré-requisitos

Conclua o tutorial de [início rápido dos aplicativos móveis](app-service-mobile-android-get-started.md) .  Essa tarefa garante que todos os pré-requisitos para o desenvolvimento de aplicativos móveis do Azure tenham sido atendidos.  O guia de início rápido também ajuda a configurar sua conta e criar seu primeiro back-end de aplicativo móvel.

Se você decidir não concluir o tutorial de início rápido, conclua as seguintes tarefas:

* [crie um back-end de aplicativo móvel][13] para usar com seu aplicativo Android.
* Em Android Studio, [atualize os arquivos de compilação gradle](#gradle-build).
* [Habilite a permissão de Internet](#enable-internet).

### <a name="gradle-build"></a>Atualizar o arquivo de compilação gradle

Altere ambos os arquivos **Build. gradle** :

1. Adicione este código ao arquivo **Build. gradle** de nível de *projeto* :

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Adicione este código ao arquivo **Build. gradle** do nível do *aplicativo de módulo* dentro da marca *Dependencies* :

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Atualmente, a versão mais recente é 3.4.0. As versões com suporte são listadas [em bintray][14].

### <a name="enable-internet"></a>Habilitar permissão de Internet

Para acessar o Azure, seu aplicativo deve ter a permissão de INTERNET habilitada. Se ainda não estiver habilitado, adicione a seguinte linha de código ao arquivo **AndroidManifest. xml** :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Criar uma conexão de cliente

Os aplicativos móveis do Azure fornecem quatro funções para seu aplicativo móvel:

* Acesso a dados e sincronização offline com um serviço de aplicativos móveis do Azure.
* Chamar APIs personalizadas escritas com o SDK do servidor de aplicativos móveis do Azure.
* Autenticação com Azure App autenticação e autorização do serviço.
* Registro de notificação por push com hubs de notificação.

Cada uma dessas funções exige primeiro que você crie um objeto `MobileServiceClient`.  Somente um objeto `MobileServiceClient` deve ser criado em seu cliente móvel (ou seja, deve ser um padrão singleton).  Para criar um objeto `MobileServiceClient`:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

O `<MobileAppUrl>` é uma cadeia de caracteres ou um objeto de URL que aponta para o back-end móvel.  Se você estiver usando Azure App serviço para hospedar seu back-end móvel, certifique-se de usar a versão segura `https://` da URL.

O cliente também requer acesso à atividade ou contexto-o parâmetro `this` no exemplo.  A construção MobileServiceClient deve acontecer dentro do método `onCreate()` da atividade referenciada no arquivo `AndroidManifest.xml`.

Como prática recomendada, você deve abstrair a comunicação do servidor em sua própria classe (padrão singleton).  Nesse caso, você deve passar a atividade dentro do construtor para configurar adequadamente o serviço.  Por exemplo:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Agora você pode chamar `AzureServiceAdapter.Initialize(this);` no método `onCreate()` de sua atividade principal.  Quaisquer outros métodos que precisam de acesso ao cliente usam `AzureServiceAdapter.getInstance();` para obter uma referência ao adaptador de serviço.

## <a name="data-operations"></a>Operações de dados

O núcleo do SDK dos aplicativos móveis do Azure é fornecer acesso aos dados armazenados em SQL Azure no back-end do aplicativo móvel.  Você pode acessar esses dados usando classes com rigidez de tipos (preferenciais) ou consultas não tipadas (não recomendado).  A massa desta seção lida com o uso de classes com rigidez de tipos.

### <a name="define-client-data-classes"></a>Definir classes de dados do cliente

Para acessar dados de tabelas SQL Azure, defina as classes de dados do cliente que correspondem às tabelas no back-end do aplicativo móvel. Os exemplos neste tópico pressupõem uma tabela chamada **MyDataTable**, que tem as seguintes colunas:

* id
* text
* concluí

O objeto do lado do cliente digitado correspondente reside em um arquivo chamado **MyDataTable. java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Adicione os métodos getter e setter para cada campo que você adicionar.  Se sua tabela de SQL Azure contiver mais colunas, você adicionaria os campos correspondentes a essa classe.  Por exemplo, se o DTO (objeto de transferência de dados) tiver uma coluna de prioridade de inteiro, você poderá adicionar esse campo, juntamente com seus métodos getter e setter:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Para saber como criar tabelas adicionais em seu back-end de aplicativos móveis, consulte [How para: Defina um controlador de tabela @ no__t-0 (back-end do .NET) ou [defina tabelas usando um esquema dinâmico][16] (back-end do node. js).

Uma tabela de back-end dos aplicativos móveis do Azure define cinco campos especiais, quatro dos quais estão disponíveis para os clientes:

* `String id`: A ID global exclusiva do registro.  Como prática recomendada, torne a ID a representação de cadeia de caracteres de um objeto [UUID][17] .
* `DateTimeOffset updatedAt`: A data/hora da última atualização.  O campo updatedAt é definido pelo servidor e nunca deve ser definido pelo seu código de cliente.
* `DateTimeOffset createdAt`: A data/hora em que o objeto foi criado.  O campo createdAt é definido pelo servidor e nunca deve ser definido pelo seu código de cliente.
* `byte[] version`: Normalmente representado como uma cadeia de caracteres, a versão também é definida pelo servidor.
* `boolean deleted`: Indica que o registro foi excluído, mas ainda não foi limpo.  Não use `deleted` como uma propriedade em sua classe.

O campo `id` é obrigatório.  O campo `updatedAt` e o campo `version` são usados para sincronização offline (para sincronização incremental e resolução de conflitos, respectivamente).  O campo `createdAt` é um campo de referência e não é usado pelo cliente.  Os nomes são nomes "através do fio" das propriedades e não são ajustáveis.  No entanto, você pode criar um mapeamento entre o objeto e os nomes "através do fio" usando a biblioteca [Gson][3] .  Por exemplo:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Criar uma referência de tabela

Para acessar uma tabela, primeiro crie um objeto [função mobileservicetable][8] chamando o método **GetTable** no [MobileServiceClient][9].  Esse método tem duas sobrecargas:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

No código a seguir, **mClient** é uma referência ao seu objeto MobileServiceClient.  A primeira sobrecarga é usada onde o nome da classe e o nome da tabela são os mesmos, e é aquele usado no início rápido:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

A segunda sobrecarga é usada quando o nome da tabela é diferente do nome da classe: o primeiro parâmetro é o nome da tabela.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Consultar uma tabela de back-end

Primeiro, obtenha uma referência de tabela.  Em seguida, execute uma consulta na referência de tabela.  Uma consulta é qualquer combinação de:

* Uma [cláusula de filtro](#filtering)`.where()`.
* Uma [cláusula de ordenação](#sorting)`.orderBy()`.
* Uma [cláusula de seleção de campo](#selection)`.select()`.
* Um `.skip()` e `.top()` para [resultados paginados](#paging).

As cláusulas devem ser apresentadas na ordem anterior.

### <a name="filter"></a>Filtrando resultados

A forma geral de uma consulta é:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

O exemplo anterior retorna todos os resultados (até o tamanho máximo de página definido pelo servidor).  O método `.execute()` executa a consulta no back-end.  A consulta é convertida em uma consulta [OData v3][19] antes da transmissão para o back-end dos aplicativos móveis.  No recebimento, o back-end dos aplicativos móveis converte a consulta em uma instrução SQL antes de executá-la na instância de SQL Azure.  Como a atividade de rede leva algum tempo, o método `.execute()` retorna um [`ListenableFuture<E>`][18].

### <a name="filtering"></a>Filtrar dados retornados

A execução da consulta a seguir retorna todos os itens da tabela **ToDoItem** em que **Complete** é igual a **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** é a referência à tabela de serviço móvel que criamos anteriormente.

Defina um filtro usando a chamada do método **Where** na referência de tabela. O método **Where** é seguido por um método de **campo** seguido por um método que especifica o predicado lógico. Os métodos de predicado possíveis incluem **EQ** (Equals), **ne** (diferente de), **gt** (maior que), **GE** (maior que ou igual a), **lt** (menor que), **Le** (menor ou igual a). Esses métodos permitem comparar campos de número e cadeia de caracteres com valores específicos.

Você pode filtrar as datas. Os métodos a seguir permitem comparar o campo de data inteiro ou as partes da data: **ano**, **mês**, **dia**, **hora**, **minuto**e **segundo**. O exemplo a seguir adiciona um filtro para os itens cuja *data de vencimento* é igual a 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Os métodos a seguir dão suporte a filtros complexos em campos de cadeia de caracteres: **StartsWith**, **EndsWith**, **concat**, **substring**, **IndexOf**, **replace**, **ToLower**, **toUpper**, **Trim**e **Length** . O exemplo a seguir filtra as linhas de tabela em que a coluna de *texto* começa com "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Os seguintes métodos de operador têm suporte em campos de número: **Add**, **sub**, **Mul**, **div**, **mod**, **Floor**, **teto**e **round**. O exemplo a seguir filtra as linhas de tabela em que a **duração** é um número par.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Você pode combinar predicados com estes métodos lógicos: **e**, **ou** e **não**. O exemplo a seguir combina dois dos exemplos anteriores.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Agrupar e aninhar operadores lógicos:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Para obter uma discussão mais detalhada e exemplos de filtragem, consulte [explorando a riqueza do modelo de consulta do cliente Android][20].

### <a name="sorting"></a>Classificar dados retornados

O código a seguir retorna todos os itens de uma tabela de **ToDoItems** classificados em ordem crescente pelo campo de *texto* . *mToDoTable* é a referência à tabela de back-end que você criou anteriormente:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

O primeiro parâmetro do método **OrderBy** é uma cadeia de caracteres igual ao nome do campo no qual classificar. O segundo parâmetro usa a enumeração **QueryOrder** para especificar se deve ser classificado em ordem crescente ou decrescente.  Se você estiver filtrando usando o método ***Where*** , o método ***Where*** deverá ser invocado antes do método ***OrderBy*** .

### <a name="selection"></a>Selecionar colunas específicas

O código a seguir ilustra como retornar todos os itens de uma tabela de **ToDoItems**, mas exibe apenas os campos **completo** e **texto** . **mToDoTable** é a referência à tabela de back-end que criamos anteriormente.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Os parâmetros para a função Select são os nomes de cadeia de caracteres das colunas da tabela que você deseja retornar.  O método **Select** precisa seguir métodos como **Where** e **OrderBy**. Ele pode ser seguido por métodos de paginação como **Skip** e **Top**.

### <a name="paging"></a>Retornar dados em páginas

Os dados são **sempre** retornados em páginas.  O número máximo de registros retornados é definido pelo servidor.  Se o cliente solicitar mais registros, o servidor retornará o número máximo de registros.  Por padrão, o tamanho máximo da página no servidor é de 50 registros.

O primeiro exemplo mostra como selecionar os cinco primeiros itens de uma tabela. A consulta retorna os itens de uma tabela de **ToDoItems**. **mToDoTable** é a referência à tabela de back-end que você criou anteriormente:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Aqui está uma consulta que ignora os cinco primeiros itens e, em seguida, retorna os cinco seguintes:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Se você quiser obter todos os registros em uma tabela, implemente o código para iterar em todas as páginas:

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Uma solicitação para todos os registros que usam esse método cria um mínimo de duas solicitações para o back-end de aplicativos móveis.

> [!TIP]
> Escolher o tamanho de página correto é um equilíbrio entre o uso de memória enquanto a solicitação está acontecendo, o uso da largura de banda e o atraso no recebimento completo dos dados.  O padrão (registros 50) é adequado para todos os dispositivos.  Se você operar exclusivamente em dispositivos de memória maiores, aumente até 500.  Descobrimos que aumentar o tamanho da página além de 500 registros resulta em atrasos inaceitáveis e grandes problemas de memória.

### <a name="chaining"></a>Como: Concatenar métodos de consulta

Os métodos usados na consulta de tabelas de back-end podem ser concatenados. Os métodos de consulta de encadeamento permitem selecionar colunas específicas de linhas filtradas que são classificadas e paginadas. Você pode criar filtros lógicos complexos.  Cada método de consulta retorna um objeto de consulta. Para encerrar a série de métodos e, na verdade, executar a consulta, chame o método **Execute** . Por exemplo:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Os métodos de consulta encadeados devem ser ordenados da seguinte maneira:

1. Métodos de filtragem (**Where**).
2. Métodos de classificação (**OrderBy**).
3. Métodos de seleção (**Select**).
4. métodos de paginação (**Skip** e **Top**).

## <a name="binding"></a>Associar dados à interface do usuário

A vinculação de dados envolve três componentes:

* A fonte de dados
* O layout da tela
* O adaptador que une os dois juntos.

Em nosso código de exemplo, retornamos os dados dos aplicativos móveis SQL Azure tabela **ToDoItem** em uma matriz. Essa atividade é um padrão comum para aplicativos de dados.  As consultas de banco de dados geralmente retornam uma coleção de linhas que o cliente obtém em uma lista ou matriz. Neste exemplo, a matriz é a fonte de dados.  O código especifica um layout de tela que define a exibição dos dados que aparecem no dispositivo.  Os dois são associados a um adaptador, que nesse código é uma extensão da classe **ArrayAdapter @ no__t-1ToDoItem @ no__t-2** .

#### <a name="layout"></a>Definir o layout

O layout é definido por vários trechos de código XML. Dado um layout existente, o código a seguir representa o **ListView** que desejamos preencher com os dados do servidor.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

No código anterior, o atributo *ListItem* especifica a ID do layout de uma linha individual na lista. Esse código especifica uma caixa de seleção e seu texto associado e é instanciado uma vez para cada item da lista. Esse layout não exibe o campo **ID** e um layout mais complexo especificaria campos adicionais na exibição. Esse código está no arquivo **row_list_to_do. xml** .

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Definir o adaptador
Como a fonte de dados de nossa exibição é uma matriz de **ToDoItem**, nós criamos uma subclasse de nosso adaptador de uma classe **ArrayAdapter @ no__t-2ToDoItem @ no__t-3** . Essa subclasse produz uma exibição para cada **ToDoItem** usando o layout **row_list_to_do** .  Em nosso código, definimos a seguinte classe que é uma extensão da classe **ArrayAdapter @ no__t-1e @ no__t-2** :

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Substitua o método **GetView** dos adaptadores. Por exemplo:

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Criamos uma instância dessa classe em nossa atividade da seguinte maneira:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

O segundo parâmetro para o Construtor ToDoItemAdapter é uma referência ao layout. Agora podemos instanciar **ListView** e atribuir o adaptador ao **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Usar o adaptador para associar à interface do usuário

Agora você está pronto para usar a associação de dados. O código a seguir mostra como obter itens na tabela e preenche o adaptador local com os itens retornados.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Chame o adaptador a qualquer momento que você modificar a tabela **ToDoItem** . Como as modificações são feitas em um registro por registro, você manipula uma única linha em vez de uma coleção. Ao inserir um item, chame o método **Add** no adaptador; ao excluir, chame o método **Remove** .

Você pode encontrar um exemplo completo no [projeto de início rápido do Android][21].

## <a name="inserting"></a>Inserir dados no back-end

Crie uma instância da classe *ToDoItem* e defina suas propriedades.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Em seguida, use **Insert ()** para inserir um objeto:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

A entidade retornada corresponde aos dados inseridos na tabela de back-end, incluindo a ID e quaisquer outros valores (como os campos `createdAt`, `updatedAt` e `version`) definidos no back-end.

As tabelas de aplicativos móveis exigem uma coluna de chave primária denominada **ID**. Essa coluna deve ser uma cadeia de caracteres. O valor padrão da coluna ID é um GUID.  Você pode fornecer outros valores exclusivos, como endereços de email ou nomes de User. Quando um valor de ID de cadeia de caracteres não é fornecido para um registro inserido, o back-end gera um novo GUID.

Os valores de ID de cadeia de caracteres fornecem as seguintes vantagens:

* As IDs podem ser geradas sem fazer uma viagem de ida e volta ao banco de dados.
* Os registros são mais fáceis de Mesclar de tabelas ou bancos de dados diferentes.
* Os valores de ID se integram melhor com a lógica de um aplicativo.

Os valores da ID da cadeia de caracteres são **necessários** para o suporte à sincronização offline.  Você não pode alterar uma ID depois que ela é armazenada no banco de dados de back-end.

## <a name="updating"></a>Atualizar dados em um aplicativo móvel

Para atualizar dados em uma tabela, passe o novo objeto para o método **Update ()** .

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Neste exemplo, *Item* é uma referência a uma linha na tabela *ToDoItem* , que teve algumas alterações feitas nele.  A linha com a mesma **ID** é atualizada.

## <a name="deleting"></a>Excluir dados em um aplicativo móvel

O código a seguir mostra como excluir dados de uma tabela especificando o objeto de dados.

```java
mToDoTable
    .delete(item);
```

Você também pode excluir um item especificando o campo **ID** da linha a ser excluída.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Pesquisar um item específico por ID

Pesquisar um item com um campo de **ID** específico com o método **lookUp ()** :

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Como: Trabalhar com dados não tipados

O modelo de programação não tipado oferece um controle exato sobre a serialização JSON.  Há alguns cenários comuns em que você talvez queira usar um modelo de programação não tipado. Por exemplo, se sua tabela de back-end contém muitas colunas e você só precisa fazer referência a um subconjunto das colunas.  O modelo tipado exige que você defina todas as colunas definidas no back-end de aplicativos móveis em sua classe de dados.  A maioria das chamadas à API para acessar dados é semelhante às chamadas de programação tipadas. A principal diferença é que, no modelo não tipado, você invoca métodos no objeto **MobileServiceJsonTable** , em vez do objeto **função mobileservicetable** .

### <a name="json_instance"></a>Criar uma instância de uma tabela não tipada

Semelhante ao modelo tipado, você começa obtendo uma referência de tabela, mas, nesse caso, é um objeto **MobileServicesJsonTable** . Obtenha a referência chamando o método **GetTable** em uma instância do cliente:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Depois de criar uma instância do **MobileServiceJsonTable**, ela tem virtualmente a mesma API disponível com o modelo de programação tipado. Em alguns casos, os métodos usam um parâmetro não tipado em vez de um parâmetro com tipo.

### <a name="json_insert"></a>Inserir em uma tabela não tipada
O código a seguir mostra como fazer uma inserção. A primeira etapa é criar um [jsonobject][1], que faz parte da biblioteca [Gson][3] .

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Em seguida, use **Insert ()** para inserir o objeto não tipado na tabela.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Se você precisar obter a ID do objeto inserido, use o método **getAsJsonPrimitive ()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Excluir de uma tabela não tipada
O código a seguir mostra como excluir uma instância, nesse caso, a mesma instância de um **jsonobject** que foi criado no exemplo de *inserção* anterior. O código é o mesmo que com o tipo de maiúsculas e minúsculas, mas o método tem uma assinatura diferente, pois faz referência a um **jsonobject**.

```java
mToDoTable
    .delete(insertedItem);
```

Você também pode excluir uma instância diretamente usando sua ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Retornar todas as linhas de uma tabela não tipada
O código a seguir mostra como recuperar uma tabela inteira. Como você está usando uma tabela JSON, você pode recuperar seletivamente apenas algumas das colunas da tabela.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

O mesmo conjunto de métodos de filtragem, filtragem e paginação que estão disponíveis para o modelo digitado estão disponíveis para o modelo não tipado.

## <a name="offline-sync"></a>Implementar sincronização offline

O SDK do cliente dos aplicativos móveis do Azure também implementa a sincronização offline de dados usando um banco de dados SQLite para armazenar uma cópia dos dados do servidor localmente.  As operações executadas em uma tabela offline não exigem que a conectividade móvel funcione.  A sincronização offline auxilia na resiliência e no desempenho às custas de lógica mais complexa para a resolução de conflitos.  O SDK do cliente dos aplicativos móveis do Azure implementa os seguintes recursos:

* Sincronização incremental: Somente os registros atualizados e novos são baixados, economizando largura de banda e consumo de memória.
* Simultaneidade otimista: As operações são consideradas com sucesso.  A resolução de conflitos é adiada até que as atualizações sejam executadas no servidor.
* Resolução de conflitos: O SDK detecta quando uma alteração conflitante foi feita no servidor e fornece ganchos para alertar o usuário.
* Exclusão reversível: Os registros excluídos são marcados como excluídos, permitindo que outros dispositivos atualizem seu cache offline.

### <a name="initialize-offline-sync"></a>Inicializar sincronização offline

Cada tabela offline deve ser definida no cache offline antes do uso.  Normalmente, a definição de tabela é feita imediatamente após a criação do cliente:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Obter uma referência à tabela de cache offline

Para uma tabela online, você usa `.getTable()`.  Para uma tabela offline, use `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Todos os métodos que estão disponíveis para tabelas online (incluindo filtragem, classificação, paginação, inserção de dados, atualização de dados e exclusão de dados) funcionam igualmente bem em tabelas online e offline.

### <a name="synchronize-the-local-offline-cache"></a>Sincronizar o cache offline local

A sincronização está dentro do controle do seu aplicativo.  Veja um exemplo de método de sincronização:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Se um nome de consulta for fornecido ao método `.pull(query, queryname)`, a sincronização incremental será usada para retornar somente os registros que foram criados ou alterados desde o último pull concluído com êxito.

### <a name="handle-conflicts-during-offline-synchronization"></a>Tratar conflitos durante a sincronização offline

Se ocorrer um conflito durante uma operação `.push()`, um `MobileServiceConflictException` será gerado.   O item emitido pelo servidor é inserido na exceção e pode ser recuperado por `.getItem()` na exceção.  Ajuste o envio por push chamando os seguintes itens no objeto MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Depois que todos os conflitos forem marcados como desejar, chame `.push()` novamente para resolver todos os conflitos.

## <a name="custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não é mapeada para uma operação de inserção, atualização, exclusão ou leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo leitura e configuração de cabeçalhos de mensagens HTTP e definição de um formato de corpo de mensagem diferente de JSON.

Em um cliente Android, você chama o método **invokeApi** para chamar o ponto de extremidade da API personalizada. O exemplo a seguir mostra como chamar um ponto de extremidade de API chamado **completeAll**, que retorna uma classe de coleção chamada **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

O método **invokeApi** é chamado no cliente, que envia uma solicitação post para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, assim como erros. Outras versões do **invokeApi** permitem que você envie opcionalmente um objeto no corpo da solicitação, especifique o método http e envie parâmetros de consulta com a solicitação. As versões não tipadas do **invokeApi** também são fornecidas.

## <a name="authentication"></a>Adicionar autenticação ao seu aplicativo

Os tutoriais já descrevem em detalhes como adicionar esses recursos.

O serviço de aplicativo dá suporte à [autenticação de usuários de aplicativos](app-service-mobile-android-get-started-users.md) usando vários provedores de identidade externos: Facebook, Google, conta da Microsoft, Twitter e Azure Active Directory. Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente a usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em seu back-end.

Há suporte para dois fluxos de autenticação: um fluxo de **servidor** e um fluxo de **cliente** . O fluxo do servidor fornece a experiência de autenticação mais simples, pois depende da interface da Web dos provedores de identidade.  Nenhum SDKs adicional é necessário para implementar a autenticação de fluxo de servidor. A autenticação de fluxo de servidor não fornece uma integração profunda ao dispositivo móvel e é recomendada somente para cenários de prova de conceito.

O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, como o logon único, pois ele depende de SDKs fornecidos pelo provedor de identidade.  Por exemplo, você pode integrar o SDK do Facebook em seu aplicativo móvel.  O cliente móvel alterna para o aplicativo do Facebook e confirma seu logon antes de alternar de volta para seu aplicativo móvel.

Quatro etapas são necessárias para habilitar a autenticação em seu aplicativo:

* Registre seu aplicativo para autenticação com um provedor de identidade.
* Configure o back-end do serviço de aplicativo.
* Restrinja as permissões de tabela a usuários autenticados somente no back-end do serviço de aplicativo.
* Adicione o código de autenticação ao seu aplicativo.

Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente a usuários autenticados. Você também pode usar o SID de um usuário autenticado para modificar solicitações.  Para obter mais informações, consulte Introdução [Introdução à autenticação] e à documentação do SDK do servidor HOWTO.

### <a name="caching"></a>Authentication Fluxo do servidor

O código a seguir inicia um processo de logon de fluxo do servidor usando o provedor do Google.  A configuração adicional é necessária devido aos requisitos de segurança para o provedor do Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Além disso, adicione o seguinte método à classe de atividade principal:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

O `GOOGLE_LOGIN_REQUEST_CODE` definido em sua atividade principal é usado para o método `login()` e dentro do método `onActivityResult()`.  Você pode escolher qualquer número exclusivo, desde que o mesmo número seja usado dentro do método `login()` e o método `onActivityResult()`.  Se você abstrair o código do cliente em um adaptador de serviço (como mostrado anteriormente), deverá chamar os métodos apropriados no adaptador de serviço.

Você também precisa configurar o projeto para customtabs.  Primeiro, especifique uma URL de redirecionamento.  Adicione o seguinte trecho a `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Adicione o **redirectUriScheme** ao arquivo `build.gradle` para seu aplicativo:

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Por fim, adicione `com.android.support:customtabs:28.0.0` à lista de dependências no arquivo `build.gradle`:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Obtenha a ID do usuário conectado de um **MobileServiceUser** usando o método **GetUserID** . Para obter um exemplo de como usar futuros para chamar as APIs de logon assíncronas, consulte Introdução [Introdução à autenticação].

> [!WARNING]
> O esquema de URL mencionado diferencia maiúsculas de minúsculas.  Verifique se todas as ocorrências de `{url_scheme_of_you_app}` Case de correspondência.

### <a name="caching"></a>Tokens de autenticação de cache

Os tokens de autenticação de cache exigem que você armazene a ID de usuário e o token de autenticação localmente no dispositivo. Na próxima vez que o aplicativo for iniciado, você verificará o cache e, se esses valores estiverem presentes, você poderá ignorar o procedimento de logon e reidratar o cliente com esses dados. No entanto, esses dados são confidenciais e devem ser armazenados criptografados para segurança caso o telefone seja roubado.  Você pode ver um exemplo completo de como armazenar em cache tokens de autenticação na [seção de tokens de autenticação de cache][7].

Ao tentar usar um token expirado, você receberá uma resposta de *401 não autorizado* . Você pode manipular erros de autenticação usando filtros.  Os filtros interceptam solicitações para o back-end do serviço de aplicativo. O código de filtro testa a resposta de um 401, dispara o processo de entrada e, em seguida, retoma a solicitação que gerou a 401.

### <a name="refresh"></a>Usar tokens de atualização

O token retornado por Azure App autenticação e autorização do serviço tem um tempo de vida definido de uma hora.  Após esse período, você deve autenticar novamente o usuário.  Se você estiver usando um token de vida útil longa recebido por meio da autenticação de fluxo de cliente, poderá autenticar novamente com Azure App autenticação de serviço e autorização usando o mesmo token.  Outro token de serviço Azure App é gerado com um novo tempo de vida.

Você também pode registrar o provedor para usar tokens de atualização.  Um token de atualização nem sempre está disponível.  É necessária uma configuração adicional:

* Para **Azure Active Directory**, configure um segredo do cliente para o aplicativo Azure Active Directory.  Especifique o segredo do cliente no serviço de Azure App ao configurar Azure Active Directory autenticação.  Ao chamar `.login()`, passe `response_type=code id_token` como um parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para o **Google**, passe o `access_type=offline` como um parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para a **conta da Microsoft**, selecione o escopo `wl.offline_access`.

Para atualizar um token, chame `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Como prática recomendada, crie um filtro que detecte uma resposta 401 do servidor e tente atualizar o token do usuário.

## <a name="log-in-with-client-flow-authentication"></a>Fazer logon com a autenticação de fluxo de cliente

O processo geral para fazer logon com a autenticação de fluxo de cliente é o seguinte:

* Configure Azure App autenticação e autorização do serviço como faria com a autenticação do servidor.
* Integre o SDK do provedor de autenticação para autenticação para produzir um token de acesso.
* Chame o método `.login()` da seguinte maneira (`result` deve ser um `AuthenticationResult`):

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Consulte o exemplo de código completo na próxima seção.

Substitua o método `onSuccess()` por qualquer código que você queira usar em um logon bem-sucedido.  A cadeia de caracteres `{provider}` é um provedor válido: **AAD** (Azure Active Directory), **Facebook**, **Google**, **MicrosoftAccount**ou **Twitter**.  Se você tiver implementado a autenticação personalizada, também poderá usar a marca do provedor de autenticação personalizada.

### <a name="adal"></a>Autenticar usuários com o Biblioteca de Autenticação do Active Directory (ADAL)

Você pode usar o Biblioteca de Autenticação do Active Directory (ADAL) para conectar usuários ao seu aplicativo usando Azure Active Directory. Usar um logon de fluxo de cliente geralmente é preferível a usar os métodos `loginAsync()`, pois ele fornece uma aparência de UX mais nativa e permite personalização adicional.

1. Configure o back-end do aplicativo móvel para entrar no AAD seguindo o tutorial [como configurar o serviço de aplicativo para Active Directory logon][22] . Certifique-se de concluir a etapa opcional de registrar um aplicativo cliente nativo.
2. Instale o ADAL modificando o arquivo Build. gradle para incluir as seguintes definições:

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Adicione o código a seguir ao seu aplicativo, fazendo as seguintes substituições:

    * Substitua **Insert-Authority-aqui** pelo nome do locatário no qual você provisionou seu aplicativo. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com.
    * Substitua **Insert-Resource-ID-aqui** pela ID do cliente para o back-end do aplicativo móvel. Você pode obter a ID do cliente na guia **avançado** em **configurações de Azure Active Directory** no Portal.
    * Substitua **Inserir-Client-ID-aqui** pela ID do cliente que você copiou do aplicativo cliente nativo.
    * Substitua **Insert-reredirect-URI-aqui** pelo ponto de extremidade */.auth/login/Done* do seu site, usando o esquema HTTPS. Esse valor deve ser semelhante a *https://contoso.azurewebsites.net/.auth/login/done* .

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Ajustar a comunicação cliente-servidor

A conexão do cliente normalmente é uma conexão HTTP básica usando a biblioteca HTTP subjacente fornecida com o SDK do Android.  Há várias razões pelas quais você desejaria alterar isso:

* Você deseja usar uma biblioteca HTTP alternativa para ajustar os tempos limite.
* Você deseja fornecer uma barra de progresso.
* Você deseja adicionar um cabeçalho personalizado para dar suporte à funcionalidade de gerenciamento de API.
* Você deseja interceptar uma resposta com falha para que possa implementar a reautenticação.
* Você deseja registrar em log as solicitações de back-end para um serviço de análise.

### <a name="using-an-alternate-http-library"></a>Usando uma biblioteca HTTP alternativa

Chame o método `.setAndroidHttpClientFactory()` imediatamente após criar a referência do cliente.  Por exemplo, para definir o tempo limite da conexão como 60 segundos (em vez do padrão de 10 segundos):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementar um filtro de progresso

Você pode implementar uma interceptação de cada solicitação implementando um `ServiceFilter`.  Por exemplo, o seguinte atualiza uma barra de progresso criada previamente:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Você pode anexar esse filtro ao cliente da seguinte maneira:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Personalizar cabeçalhos de solicitação

Use o `ServiceFilter` a seguir e anexe o filtro da mesma maneira que o `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Configurar a serialização automática

Você pode especificar uma estratégia de conversão que se aplica a todas as colunas usando a API [Gson][3] . A biblioteca de cliente Android usa [Gson][3] nos bastidores para serializar objetos Java para dados JSON antes que os dados sejam enviados para Azure app serviço.  O código a seguir usa o método **setFieldNamingStrategy ()** para definir a estratégia. Este exemplo excluirá o caractere inicial (um "m") e, em seguida, o caractere seguinte, para cada nome de campo. Por exemplo, ele transforma "mId" em "ID".  Implemente uma estratégia de conversão para reduzir a necessidade de anotações `SerializedName()` na maioria dos campos.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

Esse código deve ser executado antes de criar uma referência de cliente móvel usando o **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Introdução à autenticação]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
