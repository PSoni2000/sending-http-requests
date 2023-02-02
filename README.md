# Notes

### Sending GET request using .then()

```
function fetchMoviesHandler() {
  fetch('https://swapi.dev/api/films/')
    .then((response) => {
      return response.json();
    })
    .then((data) => {
      const transformedMovies = data.results.map((movieData) => {
        return {
          id: movieData.episode_id,
          title: movieData.title,
          openingText: movieData.opening_crawl,
          releaseDate: movieData.release_date,
        };
      });
      setMovies(transformedMovies);
    });
}
```

### Sending GET request using async-await

```
async function fetchMoviesHandler() {
    const response = await fetch('https://swapi.dev/api/films/');
    const data = await response.json();

    const transformedMovies = data.results.map((movieData) => {
      return {
        id: movieData.episode_id,
        title: movieData.title,
        openingText: movieData.opening_crawl,
        releaseDate: movieData.release_date,
      };
    });
    setMovies(transformedMovies);
  }
```

### Handling loading -

1. create loading state - `const [isLoading, setIsLoading] = useState(false);`
2. start loading before fetching data - `setIsLoading(true);`
3. end loading after data has been fetch - `setIsLoading(false);`
4. Conditionally load loading message -

```
<section>
  {!isLoading && movies.length > 0 && <MoviesList movies={movies} />}
  {!isLoading && movies.length === 0 && <p>Found no movies.</p>}
  {isLoading && <p>Loading...</p>}
</section>
```

full code -

```
function App() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);

  async function fetchMoviesHandler() {
    setIsLoading(true); // start loading
    const response = await fetch('https://swapi.dev/api/films/');
    const data = await response.json();

    const transformedMovies = data.results.map((movieData) => {
      return {
        id: movieData.episode_id,
        title: movieData.title,
        openingText: movieData.opening_crawl,
        releaseDate: movieData.release_date,
      };
    });
    setMovies(transformedMovies);
    setIsLoading(false); // end loading
  }

  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>
        {!isLoading && movies.length > 0 && <MoviesList movies={movies} />}
        {!isLoading && movies.length === 0 && <p>Found no movies.</p>}
        {isLoading && <p>Loading...</p>}
      </section>
    </React.Fragment>
  );
}
```

### Handling HTTP Errors

1. create error state - `const [error, setError] = useState(null);`
2. clear any previous error - `setError(null);`
3. create try()...catch()

   Remember! - fetch does not raise error even when status code in not 200. but axios raise error when status code is not 200

### Use Effect for requesting

```
function App() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  const fetchMoviesHandler = useCallback(async () => {
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch('https://swapi.dev/api/films/');
      if (!response.ok) {
        throw new Error('Something went wrong!');
      }

      const data = await response.json();

      const transformedMovies = data.results.map((movieData) => {
        return {
          id: movieData.episode_id,
          title: movieData.title,
          openingText: movieData.opening_crawl,
          releaseDate: movieData.release_date,
        };
      });
      setMovies(transformedMovies);
    } catch (error) {
      setError(error.message);
    }
    setIsLoading(false);
  }, []);

  useEffect(() => {
    fetchMoviesHandler();
  }, [fetchMoviesHandler]);

  let content = <p>Found no movies.</p>;

  if (movies.length > 0) {
    content = <MoviesList movies={movies} />;
  }

  if (error) {
    content = <p>{error}</p>;
  }

  if (isLoading) {
    content = <p>Loading...</p>;
  }

  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>{content}</section>
    </React.Fragment>
  );
}
```

### sending post request

```
async function addMovieHandler(movie) {
  const response = await fetch('https://react-http-6b4a6.firebaseio.com/movies.json', {
    method: 'POST',
    body: JSON.stringify(movie),
    headers: {
      'Content-Type': 'application/json'
    }
  });
  const data = await response.json();
  console.log(data);
}
```
