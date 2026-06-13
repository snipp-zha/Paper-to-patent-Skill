# Structured Draft Schema

## Purpose

Populate a UTF-8 JSON file with this structure before rendering a DOCX. Empty optional arrays are allowed. Preserve `[TO CONFIRM: ...]` markers where facts remain unresolved.

```json
{
  "title": "一种……方法、设备及介质",
  "metadata": {
    "source": "paper.pdf",
    "target": "中国发明专利",
    "draft_status": "供发明人及专利代理师复核"
  },
  "source_analysis": {
    "contains_core_formulas": true,
    "formula_count_in_source": 18,
    "contains_methodology_figures": true
  },
  "abstract_figure_number": 1,
  "assumptions": [
    "目标法域为中国"
  ],
  "invention_concept": {
    "technical_problem": "……",
    "technical_means": "……",
    "technical_effect": "……"
  },
  "evidence_ledger": [
    {
      "id": "F1",
      "feature": "……",
      "source_location": "第3页，2.2节",
      "technical_role": "……",
      "effect": "……",
      "support_status": "explicit"
    }
  ],
  "claims": [
    {
      "number": 1,
      "text": "一种……方法，其特征在于，包括：……"
    },
    {
      "number": 2,
      "text": "根据权利要求1所述的方法，其特征在于，……"
    }
  ],
  "figures": [
    {
      "number": 1,
      "title": "方法流程图",
      "type": "flowchart",
      "orientation": "vertical",
      "claim_number": 1,
      "complete_claim_flow": true,
      "nodes": [
        {
          "id": "S1",
          "label": "S1：获取并预处理待检测数据",
          "claim_step": "S1"
        },
        {
          "id": "S2",
          "label": "S2：提取多尺度特征",
          "claim_step": "S2"
        }
      ],
      "edges": [
        {
          "from": "S1",
          "to": "S2",
          "label": ""
        }
      ]
    },
    {
      "number": 2,
      "title": "核心方法结构示意图",
      "type": "methodology",
      "orientation": "horizontal",
      "nodes": [
        {
          "id": "input",
          "label": "输入特征"
        },
        {
          "id": "module",
          "label": "核心处理模块"
        },
        {
          "id": "output",
          "label": "输出特征"
        }
      ],
      "edges": [
        {
          "from": "input",
          "to": "module",
          "label": ""
        },
        {
          "from": "module",
          "to": "output",
          "label": ""
        }
      ]
    }
  ],
  "specification": {
    "technical_field": [
      "本发明涉及……"
    ],
    "background": [
      "……"
    ],
    "invention_content": {
      "problem": [
        "……"
      ],
      "solution": [
        "……"
      ],
      "beneficial_effects": [
        "……"
      ]
    },
    "figure_descriptions": [
      "图1为本发明方法流程图。"
    ],
    "equations": [
      {
        "number": 1,
        "source_location": "论文第4页，公式(1)",
        "expression": "O_u = (1/|S_u|) Σ_(x_i,y_i∈S_u) h_γ(x_i)",
        "latex": "O_u = \\frac{1}{|S_u|}\\sum_{(x_i,y_i)\\in S_u} h_\\gamma(x_i)",
        "description": "其中，O_u表示类别u的类别原型，S_u表示类别u的支持集，h_γ表示监督特征提取器。该公式通过对同一类别样本的监督特征求均值获得类别原型。"
      }
    ],
    "embodiments": [
      {
        "heading": "实施例1",
        "paragraphs": [
          "……"
        ]
      }
    ]
  },
  "abstract": "本发明涉及……",
  "audit": {
    "support_findings": [
      "……"
    ],
    "consistency_findings": [
      "……"
    ]
  },
  "inventor_questions": [
    "[TO CONFIRM: ……]"
  ]
}
```

## Rules

- Use integer claim numbers in ascending order.
- Store claim text without repeating the number at its beginning.
- Use arrays for paragraphs to preserve paragraph boundaries.
- Use only `explicit`, `inherent`, `needs-confirmation`, or `unsupported` as evidence status.
- Exclude unsupported features from formal claims.
- Keep internal audit material in the appendix, not in the formal application sections.
- Number figures consecutively from 1.
- Set `abstract_figure_number` to the main figure used as the abstract figure.
- Reuse that exact figure in the specification; do not create a conflicting duplicate.
- The abstract figure should normally be an overall method or system flow that represents the principal independent claim.
- Use `flowchart` as the figure type and `vertical` or `horizontal` as the orientation.
- Use `methodology` for an intermediate architecture, module, feature-flow, or loss-relationship figure.
- A methodology figure does not require `claim_number`, `claim_step`, or `complete_claim_flow`.
- Prefer a paper figure when suitable; otherwise redraw it as a concise black-and-white patent figure.
- Give every node a unique ASCII identifier.
- Keep `claim_step` equal to the corresponding identifier in the method claim, such as `S1`.
- Set `claim_number` to the method claim represented by the figure.
- Set `complete_claim_flow` to `true` for an overall flowchart that must cover every numbered step in that claim.
- Reference every figure in `specification.figure_descriptions`.
- Use concise node labels; put implementation detail in the specification.
- If the paper contains formulas that define core technical operations, populate `specification.equations`.
- Set `source_analysis.contains_core_formulas` after reviewing the paper.
- Number equations consecutively from 1.
- Add a valid `latex` field to every equation; the renderer converts it to editable Office Math.
- Treat `expression` as a readable audit copy, not as the DOCX rendering source.
- Record the paper page and original formula number in `source_location`.
- Define every symbol in `description` and state the technical operation performed by the formula.
- Include formulas in the standalone specification DOCX; do not place them only in an internal appendix.
