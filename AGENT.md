# 🤖 Documentação Técnica do Agente Ninja Low Price

## 📋 Informações Gerais

| Propriedade | Valor |
|-------------|-------|
| **Nome do Agente** | ninja-low-price |
| **Modelo de IA** | gpt-4.1-nano |
| **Plataforma** | Azure AI Foundry |
| **Tipo de Implementação** | OpenAPI Tool Integration |

---

## 🎯 Objetivo do Agente

O **Ninja Low Price** foi desenvolvido para atuar como um **comparador inteligente de preços em tempo real**, capaz de:

1. Receber o nome de um produto do usuário
2. Realizar buscas automáticas em lojas diferentes
3. Coletar preço base, frete e links das ofertas
4. Calcular preço total (base + frete)
5. Apresentar tabela comparativa organizada
6. Recomendar automaticamente a melhor oferta

---

## 📝 Instruções do Sistema (System Prompt)

O agente foi configurado com as seguintes instruções:
O agente de IA deve receber o nome de um produto solicitado pelo usuário e realizar
automaticamente uma busca de preços em cinco lojas diferentes. Ele deve pesquisar o
produto em lojas como Amazon, Mercado Livre, Magalu, Americanas, Casas Bahia,
Submarino, Shopee ou outras lojas relevantes.

Para cada loja, o agente deve coletar:

Preço base do produto

Identificar se existe frete grátis ou o valor do frete (quando disponível)

Link ou identificação da oferta

Em seguida, o agente deve calcular o preço total somando o preço base ao custo do
frete, considerando frete grátis como frete igual a zero.

Depois de obter os resultados, o agente deve organizar todas as informações em uma
tabela de comparação contendo:

1-Nome da loja

2-Preço base

3-Valor do frete

4-Preço total

5-Link da oferta

A tabela deve sempre conter cinco lojas — exceto nos casos em que o produto realmente
não for encontrado em todas elas. Se alguma loja não apresentar informações completas
de frete, o agente deve indicar "frete não informado".

Com base na tabela, o agente deve identificar automaticamente qual loja oferece o
menor preço total e destacar essa opção ao final da resposta, explicando o motivo
(ex: frete grátis, menor preço base ou melhor valor final).

O agente só deve recomendar a loja após exibir a tabela de comparação completa.
Caso o produto não seja encontrado em nenhuma loja, ele deve informar ao usuário
que o produto não foi localizado e sugerir que o usuário forneça mais detalhes.

A resposta final do agente deve sempre seguir esta ordem:

Mostrar o nome do produto pesquisado

Apresentar a tabela de comparação das cinco lojas

Analisar os resultados

Indicar qual loja possui o melhor preço total

Esse é o comportamento padrão que o agente deve seguir em todas as consultas realizadas.

---

## 🔧 Ações (Tools) Configuradas

### Tool: `apibusca` (Google Custom Search)

**Tipo:** OpenAPI Specification  
**Operação:** `buscarPrecos`  
**Descrição:** Busca preços de produtos em lojas online brasileiras

**Parâmetros:**

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `key` | string | ✅ Sim | API Key do Google Custom Search |
| `cx` | string | ✅ Sim | Search Engine ID |
| `q` | string | ✅ Sim | Termo de busca (nome do produto) |
| `num` | integer | ❌ Não | Quantidade de resultados (padrão: 10) |
| `gl` | string | ❌ Não | País da busca (padrão: "br") |
| `hl` | string | ❌ Não | Idioma (padrão: "pt") |

**Resposta da API:**
{
"items": [
{
"title": "Título do produto",
"link": "URL da oferta",
"snippet": "Descrição com preço",
"displayLink": "nomeloja.com.br"
}
],
"searchInformation": {
"totalResults": "número de resultados"
}
}

---

## 🔄 Fluxo de Execução

graph TD
A[Usuário pergunta o preço] --> B[Agente recebe consulta]
B --> C[Chama tool: buscarPrecos]
C --> D[Google Custom Search API]
D --> E[Busca em lojas brasileiras]
E --> F[Retorna até 10 resultados]
F --> G[Agente processa dados]
G --> H[Extrai: preço, frete, loja]
H --> I[Calcula preço total]
I --> J[Monta tabela comparativa]
J --> K[Identifica melhor oferta]
K --> L[Apresenta resposta ao usuário]

---

## 🎯 Exemplos de Uso

### Exemplo 1: Busca Bem-Sucedida

**Entrada:**

**Processamento:**
1. Agente chama `buscarPrecos` com `q="echo show preço comprar"`
2. API retorna 10 resultados de diferentes lojas
3. Agente extrai informações de 5 lojas principais
4. Calcula preços totais
5. Monta tabela comparativa

**Saída:**
- Tabela com  lojas
- Análise dos preços
- Recomendação da melhor oferta

---

## 📊 Limitações Conhecidas

1. **Dependência da API:** Limitado a 100 consultas/dia no plano gratuito do Google
2. **Extração de Preços:** Depende da qualidade do snippet retornado
3. **Frete:** Nem sempre disponível nos resultados da busca
4. **Lojas Cobertas:** Limitado às lojas indexadas pelo Google
5. **Preços em Tempo Real:** Podem estar desatualizados dependendo da indexação

---

## 🔄 Melhorias Futuras

- [ ] Adicionar cache de resultados recentes
- [ ] Integrar APIs diretas das lojas (quando disponíveis)
- [ ] Implementar histórico de preços
- [ ] Adicionar alertas de promoções
- [ ] Expandir para outras categorias de produtos

---

## 📚 Referências Técnicas

- [Azure AI Agents Documentation](https://learn.microsoft.com/azure/ai-services/agents/)
- [OpenAPI 3.0 Specification](https://swagger.io/specification/)
- [Google Custom Search JSON API](https://developers.google.com/custom-search/v1/overview)

---

**Última atualização:** Novembro 2025


