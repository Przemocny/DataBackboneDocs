# Prompty używane podczas analizy struktur danych

## 1. Generowanie wskaźników na podstawie struktur danych

```markdown


Będziesz analizować surowe struktury danych dla klienta.
Napiszesz listę potencjalnych współczynników, metryk, które z punktu widzenia biznesu będą potrzebne.

Struktura nr 1 - SampleCustomerTasks:
{
        "id": "86byf6a2v",
        "url": "https://app.clickup.com/t/86byf6a2v",
        "list": {
            "id": "901503260052",
            "name": "Taski",
            "access": true
        },
        "name": "analiza SSOT: potencjalna struktura danych",
        "tags": [],
        "space": {
            "id": "90151384174"
        },
        "folder": {
            "id": "90152051126",
            "name": "Plona Consulting ",
            "hidden": false,
            "access": true
        },
        "parent": null,
        "points": null,
        "status": {
            "status": "zakończone",
            "id": "c90152051126_SM0xvYxN",
            "color": "#008844",
            "type": "done",
            "orderindex": 5
        },
        "creator": {
            "id": 62683896,
            "username": "Usprawniacze Firm",
            "color": "",
            "email": "kontakt@usprawniaczefirm.pl",
            "profilePicture": null
        },
        "project": {
            "id": "90152051126",
            "name": "Plona Consulting ",
            "hidden": false,
            "access": true
        },
        "sharing": {
            "public": false,
            "public_share_expires_on": null,
            "public_fields": [
                "assignees",
                "priority",
                "due_date",
                "content",
                "comments",
                "attachments",
                "customFields",
                "subtasks",
                "tags",
                "checklists",
                "coverimage"
            ],
            "token": null,
            "seo_optimized": false
        },
        "team_id": "9015418181",
        "archived": false,
        "due_date": null,
        "priority": null,
        "watchers": [
            {
                "id": 62683896,
                "username": "Usprawniacze Firm",
                "color": "",
                "initials": "UF",
                "email": "kontakt@usprawniaczefirm.pl",
                "profilePicture": null
            },
            {
                "id": 38457152,
                "username": "apifex@gmail.com",
                "color": "#ff5251",
                "initials": "A",
                "email": "apifex@gmail.com",
                "profilePicture": null
            }
        ],
        "assignees": [
            {
                "id": 38457152,
                "username": "apifex@gmail.com",
                "color": "#ff5251",
                "initials": "A",
                "email": "apifex@gmail.com",
                "profilePicture": null
            }
        ],
        "custom_id": null,
        "date_done": null,
        "locations": [],
        "checklists": [],
        "orderindex": "119424322.00033590000000000000000000000000",
        "start_date": null,
        "time_spent": 10800000,
        "date_closed": null,
        "description": "",
        "__IMTINDEX__": 1,
        "date_created": "2024-04-22T19:18:51.118Z",
        "date_updated": "2024-04-30T07:56:47.659Z",
        "dependencies": [],
        "linked_tasks": [],
        "text_content": "",
        "__IMTLENGTH__": 42,
        "custom_fields": {
            "Pracownik": [
                {
                    "id": 38457152,
                    "username": "apifex@gmail.com",
                    "email": "apifex@gmail.com",
                    "color": "#ff5251",
                    "initials": "A",
                    "profilePicture": null
                }
            ]
        },
        "time_estimate": 0,
        "custom_item_id": 0,
        "group_assignees": [],
        "permission_level": "create",
        "custom_fields_original": [
            {
                "id": "470c2681-e6ec-4e52-845c-4cfac15e32a2",
                "name": "Komentarz",
                "type": "text",
                "type_config": {
                    "ai": {
                        "format": "bullet",
                        "source": "summary"
                    }
                },
                "date_created": "2024-04-05T16:57:12.168Z",
                "hide_from_guests": false,
                "required": false
            },
            {
                "id": "55e6a7a8-4465-4f12-b4f7-1f2a76ecdbe8",
                "name": "Estymata",
                "type": "drop_down",
                "type_config": {
                    "new_drop_down": true,
                    "options": [
                        {
                            "id": "a82a9299-2ad5-4e1f-9058-2f44fb961e71",
                            "name": "1-2h",
                            "color": null,
                            "orderindex": 0
                        },
                        {
                            "id": "ecef5947-0613-4753-b6ca-f2567b11d053",
                            "name": "3-6h",
                            "color": null,
                            "orderindex": 1
                        },
                        {
                            "id": "93f9d5ed-3068-4284-acf4-cf0f5a6fa4ec",
                            "name": "7-11h",
                            "color": null,
                            "orderindex": 2
                        },
                        {
                            "id": "7ed96cae-4602-4c78-8125-17e88a21422f",
                            "name": "proces ciągły",
                            "color": null,
                            "orderindex": 3
                        },
                        {
                            "id": "f9fe1d7f-bed4-4322-a9f9-deb8a0bdacb6",
                            "name": "11-15h",
                            "color": null,
                            "orderindex": 4
                        },
                        {
                            "id": "6f2f2b22-878c-4a02-ab2b-38f6a5b81388",
                            "name": "16-20h",
                            "color": null,
                            "orderindex": 5
                        },
                        {
                            "id": "2a10def4-a43d-4718-9fed-9280d3777896",
                            "name": "21-25h",
                            "color": null,
                            "orderindex": 6
                        },
                        {
                            "id": "5652683a-03ad-44ef-b567-b9e9ab0428b0",
                            "name": "26-30h",
                            "color": null,
                            "orderindex": 7
                        },
                        {
                            "id": "14670781-5f06-4888-a1a4-ea7bbc64de1a",
                            "name": "31-35h",
                            "color": null,
                            "orderindex": 8
                        },
                        {
                            "id": "ded7e9a1-9b89-4843-961c-17955038b19e",
                            "name": "36-40h",
                            "color": null,
                            "orderindex": 9
                        },
                        {
                            "id": "8808f869-94c8-4141-b992-c183ce4ddf4f",
                            "name": "41-45h",
                            "color": null,
                            "orderindex": 10
                        },
                        {
                            "id": "439555f1-fe4a-402d-8548-62bf799b2387",
                            "name": "46-50h",
                            "color": null,
                            "orderindex": 11
                        }
                    ]
                },
                "date_created": "2024-04-15T10:13:43.516Z",
                "hide_from_guests": false,
                "required": true
            },
            {
                "id": "d336b204-d49e-40e5-8085-70fc48927784",
                "name": "Pracownik",
                "type": "users",
                "type_config": {
                    "single_user": false,
                    "include_groups": true,
                    "include_guests": true,
                    "include_team_members": true
                },
                "date_created": "2024-04-05T16:45:35.124Z",
                "hide_from_guests": false,
                "value": [
                    {
                        "id": 38457152,
                        "username": "apifex@gmail.com",
                        "email": "apifex@gmail.com",
                        "color": "#ff5251",
                        "initials": "A",
                        "profilePicture": null
                    }
                ],
                "required": true
            },
            {
                "id": "16bc1312-dc7e-4d96-a39e-0d1391018c1c",
                "name": "Typ zadania",
                "type": "drop_down",
                "type_config": {
                    "new_drop_down": true,
                    "options": [
                        {
                            "id": "c9d0004e-dc97-447c-a43e-81a51d1e4c47",
                            "name": "konsultacje",
                            "color": "#2ecd6f",
                            "orderindex": 0
                        },
                        {
                            "id": "8977b7b1-aa71-424b-927d-6b090b3723b1",
                            "name": "wdrożenie",
                            "color": "#0231E8",
                            "orderindex": 1
                        },
                        {
                            "id": "af21c60d-4815-4f01-a18a-429b44e2fdfd",
                            "name": "tworzenie instrukcji / raportów",
                            "color": "#f9d900",
                            "orderindex": 2
                        },
                        {
                            "id": "2696cb10-55e2-4973-b6b0-6f4410a369f8",
                            "name": "gaszenie pożarów",
                            "color": "#800000",
                            "orderindex": 3
                        },
                        {
                            "id": "f302e17a-accc-4efb-809a-7c10a35f1e15",
                            "name": "przegląd",
                            "color": "#81B1FF",
                            "orderindex": 4
                        },
                        {
                            "id": "1bc4e021-4853-4360-af79-00a625421203",
                            "name": "research",
                            "color": "#EA80FC",
                            "orderindex": 5
                        },
                        {
                            "id": "a3c6dfcb-782d-47b0-9806-b7fd126eb12e",
                            "name": "czynności operacyjne",
                            "color": "#FF7FAB",
                            "orderindex": 6
                        },
                        {
                            "id": "c02976f4-6fc8-4ba6-8189-8005f2b02255",
                            "name": "programowanie/no-code/low-code",
                            "color": "#E65100",
                            "orderindex": 7
                        },
                        {
                            "id": "f6314b2d-61c2-4a05-89c2-1fa596b25c09",
                            "name": "Inne",
                            "color": null,
                            "orderindex": 8
                        }
                    ]
                },
                "date_created": "2024-03-18T11:50:51.252Z",
                "hide_from_guests": false,
                "required": true
            }
        ]
    }


Struktura nr 2 - SampleInvoice:

{
        "id": "in_1PRfvIBYfFbLWOb7nHeZVhoC",
        "tax": null,
        "paid": true,
        "lines": {
            "object": "list",
            "data": [
                {
                    "id": "il_1PRfvIBYfFbLWOb704DdYVqK",
                    "object": "line_item",
                    "amount": 61377,
                    "amount_excluding_tax": 61377,
                    "currency": "pln",
                    "description": "1 × Support techniczny Implemo dla e-commerce (at 613.77 zł / month)",
                    "discount_amounts": [],
                    "discountable": true,
                    "discounts": [],
                    "invoice": "in_1PRfvIBYfFbLWOb7nHeZVhoC",
                    "livemode": true,
                    "metadata": {},
                    "period": {
                        "end": 1720985726,
                        "start": 1718393726
                    },
                    "plan": {
                        "id": "price_1Oc8cbBYfFbLWOb75NKXonGe",
                        "object": "plan",
                        "active": true,
                        "aggregate_usage": null,
                        "amount": 61377,
                        "amount_decimal": "61377",
                        "billing_scheme": "per_unit",
                        "created": 1706111053,
                        "currency": "pln",
                        "interval": "month",
                        "interval_count": 1,
                        "livemode": true,
                        "metadata": {
                            "cancel_early": "true",
                            "custom_id": "BRAZ",
                            "delivery_enabled": "false",
                            "has_quantity": "false",
                            "hidden": "false",
                            "name": "Brązowy (2h/msc)",
                            "net_price": "499",
                            "order": "1",
                            "shipping": "false",
                            "shipping_phone": "false",
                            "show_active_until_counter": "false",
                            "show_net_price": "false",
                            "uuid": "90c1b394-6f5b-430e-a3d9-516480dfb486",
                            "vat_rate": "23"
                        },
                        "meter": null,
                        "nickname": null,
                        "product": "prod_PR0duHXCSoUNO8",
                        "tiers_mode": null,
                        "transform_usage": null,
                        "trial_period_days": null,
                        "usage_type": "licensed"
                    },
                    "price": {
                        "id": "price_1Oc8cbBYfFbLWOb75NKXonGe",
                        "object": "price",
                        "active": true,
                        "billing_scheme": "per_unit",
                        "created": 1706111053,
                        "currency": "pln",
                        "custom_unit_amount": null,
                        "livemode": true,
                        "lookup_key": null,
                        "metadata": {
                            "cancel_early": "true",
                            "custom_id": "BRAZ",
                            "delivery_enabled": "false",
                            "has_quantity": "false",
                            "hidden": "false",
                            "name": "Brązowy (2h/msc)",
                            "net_price": "499",
                            "order": "1",
                            "shipping": "false",
                            "shipping_phone": "false",
                            "show_active_until_counter": "false",
                            "show_net_price": "false",
                            "uuid": "90c1b394-6f5b-430e-a3d9-516480dfb486",
                            "vat_rate": "23"
                        },
                        "nickname": null,
                        "product": "prod_PR0duHXCSoUNO8",
                        "recurring": {
                            "aggregate_usage": null,
                            "interval": "month",
                            "interval_count": 1,
                            "meter": null,
                            "trial_period_days": null,
                            "usage_type": "licensed"
                        },
                        "tax_behavior": "unspecified",
                        "tiers_mode": null,
                        "transform_quantity": null,
                        "type": "recurring",
                        "unit_amount": 61377,
                        "unit_amount_decimal": "61377"
                    },
                    "proration": false,
                    "proration_details": {
                        "credited_items": null
                    },
                    "quantity": 1,
                    "subscription": "sub_1OuK3mBYfFbLWOb7ojXCmseb",
                    "subscription_item": "si_PjnfFUvyNctOC0",
                    "tax_amounts": [],
                    "tax_rates": [],
                    "type": "subscription",
                    "unit_amount_excluding_tax": "61377"
                }
            ],
            "has_more": false,
            "total_count": 1,
            "url": "/v1/invoices/in_1PRfvIBYfFbLWOb7nHeZVhoC/lines"
        },
        "quote": null,
        "total": 61377,
        "charge": "ch_3PRgrYBYfFbLWOb71xGnI9aB",
        "footer": null,
        "issuer": {
            "type": "self"
        },
        "number": "87B79AA0-0080",
        "object": null,
        "status": "paid",
        "created": "2024-06-14T19:36:33.000Z",
        "currency": "pln",
        "customer": "cus_PjnfDikdUdoua2",
        "discount": null,
        "due_date": null,
        "livemode": true,
        "metadata": {},
        "subtotal": 61377,
        "attempted": true,
        "discounts": [],
        "rendering": null,
        "amount_due": 61377,
        "period_end": "2024-06-14T19:35:26.000Z",
        "test_clock": null,
        "amount_paid": 61377,
        "application": "ca_JTzTQ7BiOPPZf5fzP01hRWaI1oEClAxB",
        "description": null,
        "invoice_pdf": "https://pay.stripe.com/invoice/acct_1NXv1zBYfFbLWOb7/live_YWNjdF8xTlh2MXpCWWZGYkxXT2I3LF9RSUdTWDJzN0NQRFJTN2RucTNPRnlON09qZmw5QkFZLDEwOTE3NjAzMg02004vG70dVQ/pdf?s=ap",
        "tax_percent": null,
        "__IMTINDEX__": 1,
        "account_name": "Implemo spółka z ograniczoną odpowiedzialnością",
        "auto_advance": false,
        "effective_at": 1718397404,
        "from_invoice": null,
        "on_behalf_of": null,
        "period_start": "2024-05-14T19:35:26.000Z",
        "subscription": "sub_1OuK3mBYfFbLWOb7ojXCmseb",
        "__IMTLENGTH__": 81,
        "attempt_count": 1,
        "automatic_tax": {
            "enabled": false,
            "liability": null,
            "status": null
        },
        "custom_fields": null,
        "customer_name": "BARTOSZ MICHAŁ KAŁUŻNY",
        "shipping_cost": null,
        "transfer_data": null,
        "billing_reason": "subscription_cycle",
        "customer_email": "bartosz.kaluzny@wp.pl",
        "customer_phone": null,
        "default_source": null,
        "ending_balance": 0,
        "payment_intent": "pi_3PRgrYBYfFbLWOb71VyNoc1B",
        "receipt_number": null,
        "account_country": "PL",
        "account_tax_ids": null,
        "amount_shipping": 0,
        "latest_revision": null,
        "amount_remaining": 0,
        "customer_address": {
            "city": "",
            "country": "PL",
            "line1": "",
            "line2": "",
            "postal_code": "08110",
            "state": ""
        },
        "customer_tax_ids": [],
        "paid_out_of_band": false,
        "payment_settings": {
            "default_mandate": null,
            "payment_method_options": null,
            "payment_method_types": null
        },
        "shipping_details": null,
        "starting_balance": 0,
        "collection_method": "charge_automatically",
        "customer_shipping": null,
        "default_tax_rates": [],
        "rendering_options": null,
        "total_tax_amounts": [],
        "hosted_invoice_url": "https://invoice.stripe.com/i/acct_1NXv1zBYfFbLWOb7/live_YWNjdF8xTlh2MXpCWWZGYkxXT2I3LF9RSUdTWDJzN0NQRFJTN2RucTNPRnlON09qZmw5QkFZLDEwOTE3NjAzMg02004vG70dVQ?s=ap",
        "status_transitions": {
            "finalized_at": "2024-06-14T20:36:44.000Z",
            "paid_at": "2024-06-14T20:36:44.000Z"
        },
        "customer_tax_exempt": "none",
        "total_excluding_tax": 61377,
        "next_payment_attempt": null,
        "statement_descriptor": null,
        "subscription_details": {
            "metadata": {}
        },
        "webhooks_delivered_at": "2024-06-14T19:36:34.000Z",
        "application_fee_amount": null,
        "default_payment_method": null,
        "subtotal_excluding_tax": 61377,
        "total_discount_amounts": [],
        "last_finalization_error": null,
        "pre_payment_credit_notes_amount": 0,
        "post_payment_credit_notes_amount": 0
    }



Odpowiedz tylko listą potencjalnych współczynników, metryk, które możemy mierzyć/liczyć na podstawie podanych struktur.

Odpowiedz zarówno listą współczynników, metryk, które występują w obrębie każdej ze struktur oraz tymi, które możemy uzyskać łącząc te dane.

```