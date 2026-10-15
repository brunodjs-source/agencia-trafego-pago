# 04 · Esquemas de saída

Extraídos do objeto `S` do script `workflow-modo-god.js` (com `node`, em 06/10/2026), para que a documentação e o código não divirjam. Se mudar um esquema, mude no script e gere de novo este arquivo.

Na ferramenta Workflow, cada esquema vai em `agent(prompt, { schema })` e o subagente é obrigado a devolver o objeto validado. Na ferramenta Agent, cole o esquema no fim do prompt e peça "devolva só o JSON".

Convenções: probabilidades e confianças de 0 a 1; `faixa_min` e `faixa_max` também de 0 a 1 nos votos; datas no formato AAAA-MM-DD; ids de decisão `D1`, `D2`...; ids de opção `A`, `B`, `C`, `D`; letras de cadeira sorteadas a cada corrida.

## Mapa

| Esquema | Papel | Rodada | O que carrega |
|---|---|---|---|
| `enquadramento` | Enquadrador | R0 | decisões falsificáveis, pesos fixados, perguntas do plano, checklist do pedido, gatilhos e modo sugerido |
| `pesquisa` | Pesquisador de frente | R1 | caminho do arquivo, resumo denso, fatos-chave, lacunas |
| `fatos` | Curador de Fatos | R1 | contagens dos 4 títulos do livro de fatos; fatos_verificados = 0 gera o carimbo SEM BASE |
| `cadeira` | Cadeira | R2 | posição por decisão com probabilidade, faixa e confiança; contagem de ideias; previsões com data |
| `juiz` | Juiz | R3 | rubrica de 5 itens (0 a 5) e nota da lente (0 a 10) por letra; consensos, divergências, melhores ideias, falhas fatais |
| `revisao` | Revisor cruzado (opcional) | R3 | a mais forte, o maior ponto cego, o que todas deixaram passar |
| `opcoes` | Consolidador de opções | R4 | opções canônicas por decisão, mapeamento de cada letra, objeção alta aberta |
| `voto` | Votante (cadeira) | R4 e R5 | um voto por decisão; mudou_de_posicao e fato_novo controlam a anulação de mudança sem fato novo |
| `argumento` | Defensor e Opositor | R5 | steelman, argumento, objeções com observação e custo, onde concorda, fontes |
| `moderadorRodada` | Moderador por rodada | R5 | preferência clara (parada adaptativa), resolvidos, abertos, números conferidos, bajulação contada |
| `moderadorFinal` | Moderador, decisão do debate | R5 | opção escolhida, números, confiança honesta, descartes, condições para rever, teste mais barato, se algum dado disponível resolve agora |
| `presidente` | Presidente | R6 | recomendação ou abstenção por decisão, minoria vencedora, premissa da concordância, o que resolveria a divergência, primeira ação, critério de abandono, previsão |
| `verificacao` | Verificador adversarial | R7 | problemas com evidência, correção, severidade e tipo (sem_fonte, numero_errado, fato_externo, regra, viabilidade, contradicao, outro) |
| `redteam` | Red Team | R7 | pre-mortem, piores casos, atores irracionais, cisnes negros, premissas com nota e nível, sensibilidade com limiar de virada |
| `correcao` | Corretor | R7 | correções aplicadas e rejeitadas com motivo |
| `lacunas` | Crítico de Lacunas | R8 | lacunas novas e o que foi escrito; contagem de números sem fonte restantes (entra no rótulo Alta) |
| `preflight` | Pre-flight | R8 | peças auditadas, pendências sem correção, correções, travessões restantes |
| `rastro` | Auditor de rastros | R9 | arquivos presentes e faltando |
| `brief` | Relator | R9 | caminho do brief, linhas gravadas no diário, resumo |

## `enquadramento` · Enquadrador (R0)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "decisoes": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string"
          },
          "pergunta": {
            "type": "string"
          },
          "afirmacao_falsificavel": {
            "type": "string"
          },
          "criterio_sucesso": {
            "type": "string"
          },
          "opcoes_iniciais": {
            "type": "array",
            "items": {
              "type": "string"
            }
          },
          "decidivel": {
            "type": "boolean"
          },
          "custo_de_reverter": {
            "type": "string"
          }
        },
        "required": [
          "id",
          "pergunta",
          "afirmacao_falsificavel",
          "criterio_sucesso",
          "opcoes_iniciais",
          "decidivel",
          "custo_de_reverter"
        ]
      }
    },
    "pesos": {
      "type": "object",
      "properties": {
        "impacto": {
          "type": "number"
        },
        "probabilidade": {
          "type": "number"
        },
        "evidencia": {
          "type": "number"
        },
        "custo_reversibilidade": {
          "type": "number"
        },
        "tempo_sinal": {
          "type": "number"
        },
        "risco": {
          "type": "number"
        }
      },
      "required": [
        "impacto",
        "probabilidade",
        "evidencia",
        "custo_reversibilidade",
        "tempo_sinal",
        "risco"
      ]
    },
    "perguntas_do_plano": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "checklist_do_pedido": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "gatilhos_de_escalada": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "modo_sugerido": {
      "type": "string",
      "enum": [
        "rapido",
        "padrao",
        "god"
      ]
    }
  },
  "required": [
    "arquivo",
    "decisoes",
    "pesos",
    "perguntas_do_plano",
    "checklist_do_pedido",
    "gatilhos_de_escalada",
    "modo_sugerido"
  ]
}
```

## `pesquisa` · Pesquisador de frente (R1)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "resumo": {
      "type": "string",
      "description": "resumo denso de até 450 palavras com os números principais"
    },
    "fatos_chave": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "lacunas": {
      "type": "array",
      "items": {
        "type": "string"
      }
    }
  },
  "required": [
    "arquivo",
    "resumo",
    "fatos_chave"
  ]
}
```

## `fatos` · Curador de Fatos (R1)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "fatos_verificados": {
      "type": "integer"
    },
    "a_buscar": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "a_derivar": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "palpites": {
      "type": "integer"
    },
    "resumo": {
      "type": "string"
    }
  },
  "required": [
    "arquivo",
    "fatos_verificados",
    "a_buscar",
    "a_derivar",
    "palpites",
    "resumo"
  ]
}
```

## `cadeira` · Cadeira (R2)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "ideias_numeradas": {
      "type": "integer"
    },
    "top_ideias": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "posicoes": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "decisao": {
            "type": "string"
          },
          "posicao": {
            "type": "string"
          },
          "probabilidade_sucesso": {
            "type": "number"
          },
          "faixa_min": {
            "type": "number"
          },
          "faixa_max": {
            "type": "number"
          },
          "confianca": {
            "type": "number"
          },
          "premissas": {
            "type": "array",
            "items": {
              "type": "string"
            }
          },
          "efeito_segunda_ordem": {
            "type": "string"
          }
        },
        "required": [
          "decisao",
          "posicao",
          "probabilidade_sucesso",
          "faixa_min",
          "faixa_max",
          "confianca",
          "premissas",
          "efeito_segunda_ordem"
        ]
      }
    },
    "maior_risco": {
      "type": "string"
    },
    "resposta_pergunta_contraria": {
      "type": "string"
    },
    "previsoes": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "metrica": {
            "type": "string"
          },
          "valor": {
            "type": "string"
          },
          "data_conferencia": {
            "type": "string"
          }
        },
        "required": [
          "metrica",
          "valor",
          "data_conferencia"
        ]
      }
    },
    "resumo": {
      "type": "string"
    }
  },
  "required": [
    "arquivo",
    "ideias_numeradas",
    "top_ideias",
    "posicoes",
    "maior_risco",
    "resposta_pergunta_contraria",
    "previsoes",
    "resumo"
  ]
}
```

## `juiz` · Juiz (R3)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "notas": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "letra": {
            "type": "string"
          },
          "dados": {
            "type": "number"
          },
          "especificidade": {
            "type": "number"
          },
          "diversidade": {
            "type": "number"
          },
          "ponto_cego": {
            "type": "number"
          },
          "executabilidade": {
            "type": "number"
          },
          "nota_lente": {
            "type": "number"
          },
          "por_que": {
            "type": "string"
          }
        },
        "required": [
          "letra",
          "dados",
          "especificidade",
          "diversidade",
          "ponto_cego",
          "executabilidade",
          "nota_lente",
          "por_que"
        ]
      }
    },
    "consensos": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "divergencias": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "melhores_ideias": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "falhas_fatais": {
      "type": "array",
      "items": {
        "type": "string"
      }
    }
  },
  "required": [
    "arquivo",
    "notas",
    "consensos",
    "divergencias",
    "melhores_ideias",
    "falhas_fatais"
  ]
}
```

## `revisao` · Revisor cruzado (opcional) (R3)

```json
{
  "type": "object",
  "properties": {
    "mais_forte": {
      "type": "object",
      "properties": {
        "letra": {
          "type": "string"
        },
        "por_que": {
          "type": "string"
        }
      },
      "required": [
        "letra",
        "por_que"
      ]
    },
    "maior_ponto_cego": {
      "type": "object",
      "properties": {
        "letra": {
          "type": "string"
        },
        "ponto": {
          "type": "string"
        }
      },
      "required": [
        "letra",
        "ponto"
      ]
    },
    "o_que_todas_deixaram_passar": {
      "type": "array",
      "items": {
        "type": "string"
      }
    }
  },
  "required": [
    "mais_forte",
    "maior_ponto_cego",
    "o_que_todas_deixaram_passar"
  ]
}
```

## `opcoes` · Consolidador de opções (R4)

```json
{
  "type": "object",
  "properties": {
    "decisoes": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "decisao": {
            "type": "string"
          },
          "opcoes": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "id": {
                  "type": "string"
                },
                "texto": {
                  "type": "string"
                },
                "fora_do_obvio": {
                  "type": "boolean"
                }
              },
              "required": [
                "id",
                "texto",
                "fora_do_obvio"
              ]
            }
          },
          "mapeamento": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "letra": {
                  "type": "string"
                },
                "opcao": {
                  "type": "string"
                }
              },
              "required": [
                "letra",
                "opcao"
              ]
            }
          },
          "objecao_alta_aberta": {
            "type": "boolean"
          }
        },
        "required": [
          "decisao",
          "opcoes",
          "mapeamento",
          "objecao_alta_aberta"
        ]
      }
    }
  },
  "required": [
    "decisoes"
  ]
}
```

## `voto` · Votante (cadeira) (R4 e R5)

```json
{
  "type": "object",
  "properties": {
    "votos": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "decisao": {
            "type": "string"
          },
          "opcao": {
            "type": "string"
          },
          "probabilidade_sucesso": {
            "type": "number"
          },
          "faixa_min": {
            "type": "number"
          },
          "faixa_max": {
            "type": "number"
          },
          "confianca": {
            "type": "number"
          },
          "motivo": {
            "type": "string"
          },
          "mudou_de_posicao": {
            "type": "boolean"
          },
          "fato_novo": {
            "type": "string"
          }
        },
        "required": [
          "decisao",
          "opcao",
          "probabilidade_sucesso",
          "faixa_min",
          "faixa_max",
          "confianca",
          "motivo",
          "mudou_de_posicao",
          "fato_novo"
        ]
      }
    }
  },
  "required": [
    "votos"
  ]
}
```

## `argumento` · Defensor e Opositor (R5)

```json
{
  "type": "object",
  "properties": {
    "steelman_do_outro_lado": {
      "type": "string"
    },
    "argumento": {
      "type": "string"
    },
    "objecoes": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "objecao": {
            "type": "string"
          },
          "observacao_que_confirma_ou_derruba": {
            "type": "string"
          },
          "custo_para_obter": {
            "type": "string"
          },
          "severidade": {
            "type": "string",
            "enum": [
              "alta",
              "media",
              "baixa"
            ]
          }
        },
        "required": [
          "objecao",
          "observacao_que_confirma_ou_derruba",
          "custo_para_obter",
          "severidade"
        ]
      }
    },
    "concorda_em": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "fontes": {
      "type": "array",
      "items": {
        "type": "string"
      }
    }
  },
  "required": [
    "steelman_do_outro_lado",
    "argumento",
    "objecoes",
    "concorda_em",
    "fontes"
  ]
}
```

## `moderadorRodada` · Moderador por rodada (R5)

```json
{
  "type": "object",
  "properties": {
    "preferencia_clara": {
      "type": "boolean"
    },
    "lado": {
      "type": "string",
      "enum": [
        "defensor",
        "opositor",
        "nenhum"
      ]
    },
    "resolvidos": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "nao_resolvidos": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "numeros_conferidos": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "concordancias_sem_argumento": {
      "type": "integer"
    },
    "objecoes_descartadas_sem_observacao": {
      "type": "integer"
    },
    "resumo_para_proxima_rodada": {
      "type": "string"
    }
  },
  "required": [
    "preferencia_clara",
    "lado",
    "resolvidos",
    "nao_resolvidos",
    "numeros_conferidos",
    "concordancias_sem_argumento",
    "objecoes_descartadas_sem_observacao",
    "resumo_para_proxima_rodada"
  ]
}
```

## `moderadorFinal` · Moderador, decisão do debate (R5)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "decisao": {
      "type": "string"
    },
    "opcao_escolhida": {
      "type": "string"
    },
    "decisao_texto": {
      "type": "string"
    },
    "numeros": {
      "type": "string"
    },
    "confianca": {
      "type": "string",
      "enum": [
        "alta",
        "media",
        "baixa"
      ]
    },
    "por_que": {
      "type": "string"
    },
    "alternativas_descartadas": {
      "type": "string"
    },
    "condicoes_para_rever": {
      "type": "string"
    },
    "objecao_alta_aberta": {
      "type": "boolean"
    },
    "teste_mais_barato": {
      "type": "string"
    },
    "dado_disponivel_resolve": {
      "type": "boolean"
    }
  },
  "required": [
    "arquivo",
    "decisao",
    "opcao_escolhida",
    "decisao_texto",
    "numeros",
    "confianca",
    "por_que",
    "alternativas_descartadas",
    "condicoes_para_rever",
    "objecao_alta_aberta",
    "teste_mais_barato",
    "dado_disponivel_resolve"
  ]
}
```

## `presidente` · Presidente (R6)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "decisoes": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "decisao": {
            "type": "string"
          },
          "recomendacao": {
            "type": "string"
          },
          "opcao": {
            "type": "string"
          },
          "abstencao": {
            "type": "boolean"
          },
          "minoria_vencedora": {
            "type": "boolean"
          },
          "onde_concorda": {
            "type": "string"
          },
          "premissa_da_concordancia": {
            "type": "string"
          },
          "onde_diverge": {
            "type": "string"
          },
          "o_que_resolveria": {
            "type": "string"
          },
          "primeira_acao": {
            "type": "string"
          },
          "dono": {
            "type": "string"
          },
          "prazo_dias": {
            "type": "number"
          },
          "dado_que_mudaria": {
            "type": "string"
          },
          "criterio_abandono": {
            "type": "string"
          },
          "teste_mais_barato": {
            "type": "string"
          },
          "previsao": {
            "type": "object",
            "properties": {
              "metrica": {
                "type": "string"
              },
              "valor": {
                "type": "string"
              },
              "faixa_min": {
                "type": "string"
              },
              "faixa_max": {
                "type": "string"
              },
              "probabilidade": {
                "type": "number"
              },
              "data_conferencia": {
                "type": "string"
              }
            },
            "required": [
              "metrica",
              "valor",
              "faixa_min",
              "faixa_max",
              "probabilidade",
              "data_conferencia"
            ]
          }
        },
        "required": [
          "decisao",
          "recomendacao",
          "opcao",
          "abstencao",
          "minoria_vencedora",
          "onde_concorda",
          "premissa_da_concordancia",
          "onde_diverge",
          "o_que_resolveria",
          "primeira_acao",
          "dono",
          "prazo_dias",
          "dado_que_mudaria",
          "criterio_abandono",
          "teste_mais_barato",
          "previsao"
        ]
      }
    },
    "resumo": {
      "type": "string"
    }
  },
  "required": [
    "arquivo",
    "decisoes",
    "resumo"
  ]
}
```

## `verificacao` · Verificador adversarial (R7)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "problemas": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "secao": {
            "type": "string"
          },
          "afirmacao": {
            "type": "string"
          },
          "problema": {
            "type": "string"
          },
          "evidencia": {
            "type": "string"
          },
          "correcao": {
            "type": "string"
          },
          "severidade": {
            "type": "string",
            "enum": [
              "alta",
              "media",
              "baixa"
            ]
          },
          "tipo": {
            "type": "string",
            "enum": [
              "sem_fonte",
              "numero_errado",
              "fato_externo",
              "regra",
              "viabilidade",
              "contradicao",
              "outro"
            ]
          }
        },
        "required": [
          "secao",
          "afirmacao",
          "problema",
          "evidencia",
          "correcao",
          "severidade",
          "tipo"
        ]
      }
    }
  },
  "required": [
    "arquivo",
    "problemas"
  ]
}
```

## `redteam` · Red Team (R7)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "pre_mortem": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "piores_casos": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "decisao": {
            "type": "string"
          },
          "cenario": {
            "type": "string"
          }
        },
        "required": [
          "decisao",
          "cenario"
        ]
      }
    },
    "atores_irracionais": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "cisnes_negros": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "premissas": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "decisao": {
            "type": "string"
          },
          "premissa": {
            "type": "string"
          },
          "nota": {
            "type": "string",
            "enum": [
              "SOLIDA",
              "ABALAVEL",
              "FRAGIL"
            ]
          },
          "nivel_evidencia": {
            "type": "integer",
            "minimum": 1,
            "maximum": 4
          },
          "impacto": {
            "type": "string",
            "enum": [
              "alta",
              "media",
              "baixa"
            ]
          },
          "como_testar": {
            "type": "string"
          }
        },
        "required": [
          "decisao",
          "premissa",
          "nota",
          "nivel_evidencia",
          "impacto",
          "como_testar"
        ]
      }
    },
    "sensibilidade": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "decisao": {
            "type": "string"
          },
          "premissa": {
            "type": "string"
          },
          "valor_atual": {
            "type": "string"
          },
          "limiar_de_virada": {
            "type": "string"
          },
          "vira_na_faixa_plausivel": {
            "type": "boolean"
          }
        },
        "required": [
          "decisao",
          "premissa",
          "valor_atual",
          "limiar_de_virada",
          "vira_na_faixa_plausivel"
        ]
      }
    }
  },
  "required": [
    "arquivo",
    "pre_mortem",
    "piores_casos",
    "atores_irracionais",
    "cisnes_negros",
    "premissas",
    "sensibilidade"
  ]
}
```

## `correcao` · Corretor (R7)

```json
{
  "type": "object",
  "properties": {
    "aplicadas": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "rejeitadas": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "problema": {
            "type": "string"
          },
          "motivo": {
            "type": "string"
          }
        },
        "required": [
          "problema",
          "motivo"
        ]
      }
    }
  },
  "required": [
    "aplicadas",
    "rejeitadas"
  ]
}
```

## `lacunas` · Crítico de Lacunas (R8)

```json
{
  "type": "object",
  "properties": {
    "lacunas": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "secao": {
            "type": "string"
          },
          "lacuna": {
            "type": "string"
          },
          "o_que_foi_escrito": {
            "type": "string"
          }
        },
        "required": [
          "secao",
          "lacuna",
          "o_que_foi_escrito"
        ]
      }
    },
    "numeros_sem_fonte_restantes": {
      "type": "integer"
    }
  },
  "required": [
    "lacunas",
    "numeros_sem_fonte_restantes"
  ]
}
```

## `preflight` · Pre-flight (R8)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "pecas_auditadas": {
      "type": "integer"
    },
    "reprovadas": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "peca": {
            "type": "string"
          },
          "dimensao": {
            "type": "string"
          },
          "problema": {
            "type": "string"
          }
        },
        "required": [
          "peca",
          "dimensao",
          "problema"
        ]
      }
    },
    "corrigidas": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "travessoes_restantes": {
      "type": "integer"
    }
  },
  "required": [
    "arquivo",
    "pecas_auditadas",
    "reprovadas",
    "corrigidas",
    "travessoes_restantes"
  ]
}
```

## `rastro` · Auditor de rastros (R9)

```json
{
  "type": "object",
  "properties": {
    "presentes": {
      "type": "array",
      "items": {
        "type": "string"
      }
    },
    "faltando": {
      "type": "array",
      "items": {
        "type": "string"
      }
    }
  },
  "required": [
    "presentes",
    "faltando"
  ]
}
```

## `brief` · Relator (R9)

```json
{
  "type": "object",
  "properties": {
    "arquivo": {
      "type": "string"
    },
    "linhas_no_diario": {
      "type": "integer"
    },
    "resumo": {
      "type": "string"
    }
  },
  "required": [
    "arquivo",
    "linhas_no_diario",
    "resumo"
  ]
}
```

## Campos calculados no código (não vêm de agente)

| Campo | Como é calculado |
|---|---|
| `share` | Soma dos pesos (confiança reescalada) da opção líder dividida pela soma de todos os pesos da decisão |
| `amplitudeTop` | Maior menos menor probabilidade de sucesso entre as cadeiras que votaram na opção líder |
| `dispersao` | Maior menos menor probabilidade entre todas as cadeiras na decisão; a média entre decisões abaixo de 0,10 gera DIVERSIDADE BAIXA |
| `pool_logodds` | Média dos logit das probabilidades de quem votou na líder, convertida de volta (probabilidades limitadas a 0,01 e 0,99 antes do logit) |
| `faixa` | Menor `faixa_min` e maior `faixa_max` de quem votou na líder |
| `rachada` | `share` abaixo de 2/3, ou `amplitudeTop` acima de 0,30, ou `objecao_alta_aberta` |
| `unanime` | Todas as cadeiras na mesma opção |
| `convergiu` | Ver `05-pontuacao-e-confianca.md` seção 4 |
| `rotulo` e `motivos` | Função `rotular`; ver `05-pontuacao-e-confianca.md` seção 5 |
