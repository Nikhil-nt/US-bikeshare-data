import time
import pandas as pd


CITY_DATA = {'chicago': 'chicago.csv',
             'new york city': 'new_york_city.csv',
             'washington': 'washington.csv'}
MONTH = {'january': 1, 'february': 2, 'march': 3,
         'april': 4, 'may': 5, 'june': 6}
MONTH_BY_NUM = {1: 'january', 2: 'february', 3: 'march',
                4: 'april', 5: 'may', 6: 'june'}
DAY = {0: 'monday', 1: 'tuesday', 2: 'wednesday',
       3: 'thursday', 4: 'friday', 5: 'saturday',
       6: 'sunday'}

def get_filters():
    """
    Asks user to specify a city, month, and day to analyze.

    Returns:
        ('chicago') city - name of the city to analyze
        ('all') month - name of the month to filter by, or "all" to apply no month filter
        ('all') day - name of the day of week to filter by, or "all" to apply no day filter
    """
    print('Hello! Let\'s explore some US bikeshare data!')
    # TO DO: get user input for city. (chicago, new york city, washington). 
    # HINT: Use a while loop to handle invalid inputs

    print('Hi, please enter the name of the city')
    city = ''
    while city.lower() not in CITY_DATA.keys():
        city = input('Enter now\n')
        city = city.lower()
        if city == 'chicago':
            break
        elif city == 'new york city':
            break
        elif city == 'washington':
            break
        else:
            print('Please enter a valid city name')

    # TO DO: get user input for month (all, january, february, ... , june)
    valid_month_inputs = ['all', 'january', 'february', 'march',
                          'april', 'may', 'june']
    month = ''
    print('Hi, please enter the month to filter')
    while month.lower() not in valid_month_inputs:
        month = input('Enter now\n')
        month = month.lower()
        if month in valid_month_inputs:
            break
        else:
            print('Please enter a valid month name')

    # TO DO: get user input for day of week (all, monday, tuesday, ... sunday)
    valid_day_inputs = ['all', 'monday', 'tuesday', 'wednesday', 'thursday',
                        'friday', 'saturday', 'sunday']
    print('Hi, please enter the day of week to filter')
    day = ''
    while day.lower() not in valid_day_inputs:
        day = input('Enter now\n')
        day = day.lower()
        if day in valid_day_inputs:
            break
        else:
            print('Please enter a valid day of week name')

    return city, month, day




def load_data(city, month, day):
    """
    Loads data for the specified city and filters by month and day if applicable.

    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """
    df = pd.read_csv(CITY_DATA[city])
    df['Start Time'] = pd.to_datetime(df['Start Time'])
    df['month'] = df['Start Time'].dt.month
    df['day_of_week'] = df['Start Time'].dt.weekday_name
    df['hour'] = df['Start Time'].dt.hour
    if month != 'all':
        df = df[df['month'] == MONTH[month]]
    if day != 'all':
        df = df[df['day_of_week'] == day.title()]
    print(df)
    return df


def time_stats(df):
    """Displays statistics on the most frequent times of travel."""

    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()

    # TO DO: display the most common month
    common_month = df['month'].mode()[0]

    # TO DO: display the most common day of week
    common_day_of_week = df['day_of_week'].mode()[0]

    # TO DO: display the most common start hour
    common_start_hour = df['hour'].mode()[0]

    print('Frequent Month: ', MONTH_BY_NUM[common_month])
    print('Frequent day of week: ', common_day_of_week)
    print('Frequent start hour: ', common_start_hour)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # TO DO: display most commonly used start station
    popular_start_station = df['Start Station'].mode()[0] 


    # TO DO: display most commonly used end station
    popular_end_station = df['End Station'].mode()[0]

    # TO DO: display most frequent combination of start station and end station trip
#   popular_combination = df[popular_start_station +  popular_end_station].mode()[0]

    popular_combination = df.groupby(['Start Station', 'End Station']).size().nlargest(1)

    print('Frequent start station: ', popular_start_station)
    print('Frequent end station: ', popular_end_station)
    print('Frequent combination: ', popular_combination)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""

    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # TO DO: display total travel time
    total_time = df['Trip Duration'].sum()
    print('Total time: ', total_time)

    # TO DO: display mean travel time
    travel_time = df['Trip Duration'].mean()
    print('Mean time: ', travel_time)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def user_stats(df):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()

    # TO DO: Display counts of user types
    user_types = df['User Type'].value_counts()

    # TO DO: Display counts of gender
    gender_counts = df['Gender'].value_counts()

    # TO DO: Display earliest, most recent, and most common year of birth
    birth_counts = df['Birth Year'].value_counts()
    print(type(birth_counts))

    print('User types: \n', user_types)
    print('Gender types: \n', gender_counts)
    print('Birth counts: \n', birth_counts.head(n=10))
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)

    
def main():    
    while True:
        city, month, day = get_filters()
        print(city, month, day)
        df = load_data(city, month, day)
        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df)

        restart = input('\nWould you like to restart? Enter yes or no.\n')
        while restart.lower() not in ['yes', 'no']:
            print('invalid input. Please type yes or no.')
            restart = input('\nwould you like to restart? Type yes or no.\n')
        if restart.lower() != 'yes':
            print('thank you')
            break
           
if __name__ == "__main__":  
    main()
