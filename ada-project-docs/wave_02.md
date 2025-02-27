# Wave 2: Custom Endpoints

## `Rental` Model

These custom endpoints are best served with a `Rental` model.  You will need to determine the columns for the `Rental` table and establish the following relationships between the tables using SQLAlchemy.

![ERD Diagram](/assets/retro-video-store.svg)

*Fig. ERD Diagram of the finished database.*

## `POST /rentals/check-out`

[Checks out](https://www.merriam-webster.com/dictionary/checkout) a video to a customer, and updates the data in the database as such.

When successful, this request should:
- increase the customer's `videos_checked_out_count` by one
- decrease the video's `available_inventory` by one
- create a due date. The rental's due date is the seven days from the current date.

#### Required Request Body Parameters

Request Body Param | Type | Details
--- | --- | ---
`customer_id` | integer | ID of the customer attempting to check out this video
`video_id` | integer | ID of the video to be checked out

#### Response

Typical success response:

Status: `200`

```json
{
  "customer_id": 122581016,
  "video_id": 235040983,
  "due_date": "2020-06-31",
  "videos_checked_out_count": 2,
  "available_inventory": 5
}
```

#### Errors & Edge Cases to Check

- The API should return back detailed errors and a status `404: Not Found` if the customer does not exist
- The API should return back detailed errors and a status `404: Not Found` if the video does not exist
- The API should return back detailed errors and a status `400: Bad Request` if the video does not have any available inventory before check out

## `POST /rentals/check-in`
[Checks in](https://www.merriam-webster.com/dictionary/check-in) a video to a customer, and updates the data in the database as such.

When successful, this request should:
- decrease the customer's `videos_checked_out_count` by one
- increase the video's `available_inventory` by one

#### Required Request Body Parameters

Request Body Param | Type | Details
--- | --- | ---
`customer_id` | integer | ID of the customer attempting to check out this video
`video_id` | integer | ID of the video to be checked out

#### Response

Typical success response:

Status: `200`

```json
{
  "customer_id": 122581016,
  "video_id": 277419103,
  "videos_checked_out_count": 1,
  "available_inventory": 6
}
```

#### Errors and Edge Cases to Check

- The API should return back detailed errors and a status `404: Not Found` if the customer does not exist
- The API should return back detailed errors and a status `404: Not Found` if the video does not exist
- The API should return back detailed errors and a status `400: Bad Request` if the video and customer do not match a current rental

## `GET /customers/<id>/rentals`

List the videos a customer _currently_ has checked out

#### Required Arguments

Arg | Type | Details
--- | --- | ---
`id` | integer | The id of the customer

#### Response

Typical success response is a list of videos with the due date:

Status: `200`

```json

[
    {
        "release_date": "Wed, 01 Jan 1958 00:00:00 GMT",
        "title": "Vertigo",
        "due_date": "Thu, 13 May 2021 19:27:47 GMT",
    },
    {
        "release_date": "Wed, 01 Jan 1941 00:00:00 GMT",
        "title": "Citizen Kane",
        "due_date": "Thu, 13 May 2021 19:28:00 GMT",
    }
]

```
#### Errors and Edge Cases to Check
- The API should return back detailed errors and a status `404: Not Found` if the customer does not exist
- The API should return an empty list if the customer does not have any videos checked out.

## `GET /videos/<id>/rentals`

List the customers who _currently_ have the video checked out

#### Required Arguments

Arg | Type | Details
--- | --- | ---
`id` | integer | The id of the video

#### Response

Typical success response is a list of customers with the due date:

Status: `200`

```json
[
    {
        "due_date": "Thu, 13 May 2021 21:36:38 GMT",
        "name": "Edith Wong",
        "phone": "(555) 555-5555",
        "postal_code": "99999",
    },
    {
        "due_date": "Thu, 13 May 2021 21:36:47 GMT",
        "name": "Ricarda Mowery",
        "phone": "(555) 555-5555",
        "postal_code": "99999",
    }
]

```
#### Errors and Edge Cases to Check
- The API should return back detailed errors and a status `404: Not Found` if the video does not exist
- The API should return an empty list if the video is not checked out to any customers.
