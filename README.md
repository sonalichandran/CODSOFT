import pandas as pd
from surprise import Dataset, Reader, SVD
from surprise.model_selection import train_test_split


data = {
    'user': ['Afreen', 'Srii', 'Indhu', 'kirthi', 'Sai', 'Vridha', 'Ananya', 'Aara', 'Varunika', 'Aadhvi'],
    'item': ['Leo', 'Anandam', 'June', 'OruAdaarLove', 'Premam', 'Love', 'Akaashwaani', 'Fidaa', 'Thankyou', 'Bigil'],
    'rating': [5, 4, 3, 2, 1, 5, 7, 6, 5, 8],
}

reader = Reader(rating_scale=(1, 10)) 
df = pd.DataFrame(data)


dataset = Dataset.load_from_df(df[['user', 'item', 'rating']], reader)


trainset, testset = train_test_split(dataset, test_size=0.2, random_state=42)


model = SVD()
model.fit(trainset)


def get_top_n_recommendations(model, user_id, n=3):
    items_rated_by_user = df[df['user'] == user_id]['item'].tolist()
    items_to_predict = [item for item in df['item'].unique() if item not in items_rated_by_user]

    predictions = [(item, model.predict(user_id, item).est) for item in items_to_predict]
    predictions.sort(key=lambda x: x[1], reverse=True)

    return predictions[:n]


def get_user_preference():
    preference = input("Enter 'movie' or 'song': ")
    return preference.lower()


user_id = input("Enter your username: ")
user_preference = get_user_preference()

if user_preference == 'movie':
    top_n_recommendations = get_top_n_recommendations(model, user_id, n=3)
    print(f"\nTop 3 movie recommendations for User {user_id}:")
    for item, est_rating in top_n_recommendations:
        print(f"{item} with estimated rating: {est_rating}")
elif user_preference == 'song':
   
    print(f"\nTop 3 song recommendations for User {user_id}:")
    print("Unakenna Venum sollu")
    print("Oru naalil Vazhkai engum")
    print("Kanave kanave")
else:
    print("Invalid preference. Please enter 'movie' or 'song'.")
